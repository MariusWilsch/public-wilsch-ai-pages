---
publish: true
---

# PII Pseudonymizer Design — OpenWebUI User Protection
[[project-iitr-pii-pseudonymizer-design]]

Design for pseudonymizing user PII across three surfaces — OpenWebUI's SQLite database, all three pipeline filter Langfuse traces (Navigation, Masterfragen, Court Judgments), and historical Langfuse records — on IITR-PROD, covering batch cleanup, real-time protection via shared utility, and retroactive ClickHouse mutation.

---

## Problem Statement

OpenWebUI on IITR-PROD stores real user identities (names, emails) from Keycloak OIDC authentication in two locations:

1. **SQLite DB** — `user` and `auth` tables contain plaintext names and emails for every authenticated user
2. **Langfuse traces** — all three OpenWebUI pipeline filters (Navigation, Masterfragen, Court Judgments) pass raw `user["name"]` or `user["email"]` as `user_id` to Langfuse via `update_current_trace()`, creating traceable PII in observability data

A simplified pseudonymization script exists (`masterfragen/package/pseudonymize_simple.py`, 104 lines) but is hardcoded to the dev environment and has never been executed on production — the prod DB contains 63 users, all with plaintext PII. An older, complex protection system (`masterfragen/src/utils/protection.py`) coexists in the repo but is not imported anywhere. Neither system is active.

**PII regression (PR #30, 2026-04-01):** The migration from per-project Flask proxy to shared OpenWebUI Pipelines sidecar changed what user identity reaches Langfuse. The old Flask proxy sent an opaque UUID (`X-OpenWebUI-User-Id` header) with `mask_sensitive_prompts` enabled. The new pipeline filters extract `user["name"]` or `user["email"]` from the Pipelines framework `user` dict and pass it unhashed, unmasked. All three filters share identical PII-exposing code.

**Preconditions:**
- IITR uses Keycloak SSO (`sso.iitr.de`) for OpenWebUI authentication — real PII enters through OIDC tokens
- OpenWebUI (v0.8.10 staging, v0.6.17 prod) is deployed as an unmodified upstream container — PII enters at OIDC login and is written to SQLite before any filter can intercept
- Three pipeline filters (Navigation, Masterfragen, Court Judgments) run in a shared Pipelines sidecar container, each performing Langfuse tracing with unmasked user identity
- Historical Langfuse traces already contain unhashed names/emails from prior usage
- GDPR compliance requires pseudonymization of non-admin user data before broader rollout

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | All non-admin OIDC user PII is pseudonymized across three surfaces (SQLite DB, pipeline filter Langfuse traces, historical Langfuse records) on IITR-PROD, with automated protection for new users across all three pipeline filters |
| **Success** | (1) Existing unhashed user records are batch-cleaned in SQLite. (2) All three pipeline filters (Navigation, Masterfragen, Court Judgments) produce pseudonymized identities in Langfuse from first request. (3) Historical Langfuse traces are retroactively cleaned. (4) Admin/developer users retain real identities for operational visibility |
| **Done test** | A test OIDC user logs into OpenWebUI on IITR-PROD → queries any of the three pipeline models → name/email appears hashed in both SQLite DB and Langfuse traces → whitelisted admin accounts remain identifiable in both |

---

## Approach

### Part 1: Batch Pseudonymizer — Environment-Aware Script

The existing `pseudonymize_simple.py` (104 lines) provides the core logic: copy SQLite DB from Docker container → SHA-256 hash non-whitelisted user names/emails → copy DB back. Three changes make it production-ready:

**1a. Environment parameterization:**
- Container name derived from `COMPOSE_PROJECT_NAME` env var: `${COMPOSE_PROJECT_NAME}-openwebui-1` (e.g., `iitr-staging-openwebui-1`, `iitr-prod-openwebui-1`)
- `PSEUDONYMIZE_ENV` env var (already exists in the script) selects the whitelist
- DB working path: `/tmp/webui_{env}.db` (transient) — backup path: `/home/shared/backups/` (persistent, survives reboots)

**1b. Scheduled execution:**
- GHA scheduled workflow (daily at 03:00 UTC) — the CCI #646 convention requires all server operations to run through GHA as the `deploy` user. The `agent` user has read-only docker access (`POST=0` socket proxy) and cannot execute `docker cp` or `docker restart`
- Workflow SSHs as `deploy`, runs the batch script against the running OpenWebUI container, logs results to GHA
- Script is idempotent: guard clause skips already-pseudonymized records matching `@anon.de` (simplified script format) and `@pseudonym.local` (legacy protection system format found in prod auth table)

**1c. Old code cleanup:**
- Remove `masterfragen/src/utils/protection.py` (227 lines, not imported anywhere, dead code)
- Remove `masterfragen/package/README.md` (documents the deprecated complex system)
- Remove `masterfragen/app/` directory (Flask proxy — replaced by pipeline filters, dead code since PR #30)
- Remove old per-env compose files (`docker-compose.dev.yml`, `docker-compose.staging.yml`, `docker-compose.prod.yml` under `masterfragen/`) — superseded by root compose with CCI #646 infra/app split
- Keep `test_prod_pseudonymize.py` as a dry-run validation tool

### Part 2: Pipeline Filter Pseudonymization — Shared Utility Across All Filters

All three pipeline filters extract user identity in their `inlet()` method and pass it unhashed to Langfuse. The interception point and code pattern are identical across filters:

```python
# Current code in all three filters (identical)
if user:
    user_name = user.get("name") or user.get("email")
# ...
self.langfuse.update_current_trace(
    name="...",
    user_id=user_name,  # ← raw PII to Langfuse
)
```

**Shared utility (`pii_utils.py`):**
A single module consumed by all three filters, placed at the repo root (accessible to all project directories via the shared Pipelines container volume mount):

- `pseudonymize_user_id(email: str, name: str) -> str` — checks `email` against `PROTECTED_EMAILS` env var. If protected, returns `name` (preserving admin visibility in Langfuse). If not, returns `SHA-256(email)[:16]@anon.de`
- The check is always against `email` (from `user["email"]`), not `name` — email is the stable identifier from OIDC, names can vary
- The return value replaces `user_name` in `update_current_trace(user_id=...)`

**Filter changes (all three):**
- `navigation/pipelines/typesense_rag_filter.py`
- `masterfragen/pipelines/masterfragen_hybrid_rag_filter.py`
- `court-judgments/pipelines/urteile_hybrid_rag_filter.py`

Each filter's `inlet()` replaces the raw `user_name` extraction with a call to the shared utility. The change is ~3 lines per filter.

**Incidental fix:** Court Judgments filter reads `LANGFUSE_SECRET_KEY` / `LANGFUSE_PUBLIC_KEY` instead of its dedicated `CJ_LANGFUSE_SECRET_KEY` / `CJ_LANGFUSE_PUBLIC_KEY` — writing traces to the shared Navigation project rather than its own. Fix during this pass.

### Part 3: Whitelist Management — Environment-Variable-Driven

Both the batch script and the pipeline filter utility need a shared whitelist of protected users. Currently the whitelist is hardcoded in `pseudonymize_simple.py` — this needs to become configurable.

**Design:**
- Single env var `PROTECTED_EMAILS` — comma-separated list of email addresses that should NOT be pseudonymized
- Read by both the batch script (Part 1) and `pii_utils.py` (Part 2)
- Defined in `.env.config` (committed, per CCI #646 convention — replaces the old per-env `.env.dev`/`.env.staging`/`.env.prod` pattern). `PROTECTED_EMAILS` is configuration, not a secret — it belongs in `.env.config`, not GitHub Secrets
- The `pii_utils.py` shared utility (Part 2) provides `pseudonymize_user_id()` — consumed by pipeline filters at runtime. The batch script imports the same logic for consistency

**Current known protected users:**
- `stellmacher@iitr.de` — Marvin Stellmacher (IITR technical contact)
- `skraska@iitr.de` — Sebastian Kraska (IITR)
- `marius.santiago.wilsch@gmail.com` — Marius Wilsch
- `fabian.doerringer@tngtech.com` — IITR Admin
- `test@test.de`, `admin@admin.de` — test accounts

**Undefined:** Whitelist pending update from Marius. Current list is a snapshot. Note: Roman Rolnik (`holorolnik@gmail.com`) is still in the batch script's hardcoded whitelist and present in the prod DB with plaintext data — must be removed from the whitelist so his records get pseudonymized.

### Part 4: Deployment & Verification

Deployment follows CCI #646 conventions: push=preview for code changes, GHA scheduled workflow for batch operations.

**Pipeline filter deployment (Part 2 changes):**
- Changes to pipeline filter `.py` files and the new `pii_utils.py` deploy automatically via push=preview
- Push to branch → GHA `deploy-action` triggers → filters reload in shared Pipelines container (bind-mounted `.py` files)
- Verify: query each pipeline model with a test user → check Langfuse trace shows hashed `user_id`

**Batch script deployment (Part 1):**
- GHA scheduled workflow (`.github/workflows/pseudonymize.yml`) runs daily at 03:00 UTC
- SSHs as `deploy` user → runs `pseudonymize_simple.py` against the OpenWebUI container → logs to GHA
- Backup stored in `/home/shared/backups/webui_{env}_backup_{timestamp}.db` on host (bind-mounted volume)

**Rollback:**
- Batch: restore from backup file — `docker cp /home/shared/backups/{backup}.db container:/app/backend/data/webui.db` + container restart (via GHA as `deploy` user)
- Pipeline filters: revert commit + push (triggers automatic redeploy via deploy-action)

**Verification prerequisite:** Marius has copied the prod database to staging (`masterfragen/data/webui-prod.db`, committed in `a9531e4`). Note: prod runs OpenWebUI 0.6.17, staging runs 0.8.10 — the `user` and `auth` table schemas are compatible (both use `name`, `email` VARCHAR fields), but other tables may differ.

### Part 5: Langfuse Historical Trace Cleanup

Parts 1-2 prevent new PII from entering SQLite and Langfuse. Existing Langfuse traces already contain unhashed names/emails from all prior usage — these need retroactive cleanup.

**Scope:**
- All traces in the IITR Langfuse project(s) where `user_id` contains a non-hashed value (not ending in `@anon.de`)
- Applies to traces from all three pipeline filters (Navigation, Masterfragen, Court Judgments)
- Whitelisted users' traces remain unchanged

**Mechanism — Direct ClickHouse mutation:**
The Langfuse REST API has no PATCH endpoint for traces — `user_id` cannot be updated via the API. However, Langfuse v3 stores traces in ClickHouse (v26.3, already deployed in `docker-compose.infra.yml`), which supports `ALTER TABLE ... UPDATE` mutations natively. This is the least destructive approach: it updates `user_id` in place without deleting traces or re-ingesting spans.

```sql
-- Preview affected traces
SELECT user_id, count() FROM traces
WHERE user_id NOT IN ({whitelist_emails})
  AND user_id != '' AND user_id IS NOT NULL
  AND NOT endsWith(user_id, '@anon.de')
GROUP BY user_id;

-- Execute pseudonymization
ALTER TABLE traces UPDATE
  user_id = substring(hex(SHA256(user_id)), 1, 16) || '@anon.de'
WHERE user_id NOT IN ({whitelist_emails})
  AND user_id != '' AND user_id IS NOT NULL
  AND NOT endsWith(user_id, '@anon.de');
```

ClickHouse mutations are asynchronous — verify completion via `SELECT * FROM system.mutations WHERE is_done = 0`. Run as `deploy` user via GHA (same access pattern as Part 1).

**Alternatives considered and rejected:**
- **Ingestion-based upsert** (`POST /api/public/ingestion` with same trace ID) — updates the trace record but requires knowing every trace ID. Impractical for bulk cleanup.
- **Bulk delete** (`DELETE /api/public/traces`) — loses all observability history. Unacceptable.

**Constraint:** Prior investigation (#1297) found that initializing a second Langfuse client in the shared Pipelines Python process causes `@observe()` to silently skip tracing (cross-project leakage guard). This does not affect the cleanup script — it runs as a standalone operation, not inside the Pipelines container.

---

## Source

- **Code (current):** [iitr-platform/staging](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/staging) — mono-repo with all three pipeline filters, batch script, and CCI #646 compose split
- **Pipeline filters:** `navigation/pipelines/typesense_rag_filter.py`, `masterfragen/pipelines/masterfragen_hybrid_rag_filter.py`, `court-judgments/pipelines/urteile_hybrid_rag_filter.py`
- **Batch script:** `masterfragen/package/pseudonymize_simple.py`
- **Prod DB snapshot:** `masterfragen/data/webui-prod.db` (OpenWebUI 0.6.17, 63 users, commit `a9531e4`)
- **Deploy convention:** [CCI #646 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-runtime-state-design)
- **Deploy infra:** [deploy-action](https://github.com/WILSCH-AI-SERVICES/deploy-action) — shared GHA composite
- **Issue:** [#1100](https://github.com/DaveX2001/deliverable-tracking/issues/1100)
- **Epic:** [#959](https://github.com/DaveX2001/deliverable-tracking/issues/959) — IITR Contract Extension & Stabilization
- **Session:** `75e3c02c-7e7a-406d-bbc4-e3798445e438` (2026-03-29 extraction pass)
- **Session:** `fc34fe77-e32c-40e2-a23d-211273428f1f` (2026-03-30 SA feedback resolution — F1: closed-source→upstream, F2: Makefile→COMPOSE_PROJECT_NAME, F3: whitelist Undefined)
- **Session:** `6bb689e1-1e1e-493d-8983-ef6f3eb45451` (2026-04-01 fresh design pass — Flask proxy→pipeline filters, PII regression discovery, CCI #646 alignment, scope expansion to all 3 filters)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

