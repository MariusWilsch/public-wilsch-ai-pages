---
publish: true
---

# PII Pseudonymizer Design — OpenWebUI User Protection
[[project-iitr-pii-pseudonymizer-design]]

Design for pseudonymizing user PII in OpenWebUI's SQLite database and eliminating the Langfuse trace PII surface on IITR-PROD. Batch cleanup of existing DB records, scheduled re-runs for new OIDC users, and full removal of the Langfuse stack (replaced by native OpenWebUI observability).

---

## Problem Statement

OpenWebUI on IITR-PROD stores real user identities (names, emails) from Keycloak OIDC authentication in its SQLite database — `user` and `auth` tables contain plaintext names and emails for every authenticated user. The prod DB (v0.6.17, 63 users) contains unprotected PII for non-admin OIDC users (e.g., Lisa Milbredt, Ralf Zlamal) alongside whitelisted admin accounts.

A simplified pseudonymization script exists (`masterfragen/package/pseudonymize_simple.py`, 104 lines) but is hardcoded to the dev environment and has never been executed on production. An older protection system (`masterfragen/src/utils/protection.py`, 227 lines) coexists but is dead code — not imported anywhere. Evidence of both systems in the DB: one user record has hex-encoded name/email from the old system, while no `@anon.de` format records exist (simplified script never ran).

**Observability context (#1297, closed 2026-04-02):** Custom Langfuse pipeline tracing was scheduled for removal in #1297 but its implementation PR (#32) was never merged — the Langfuse stack (6 services) and inline tracing code (~100-135 lines across 3 pipeline filters + `langfuse_utils.py`) still run on staging/prod. **Langfuse removal is part of this scope (see Part 2)** — Langfuse traces are a secondary PII surface (real user emails in trace metadata), and eliminating the stack entirely closes that surface rather than patching it. Native OpenWebUI observability (Analytics dashboard, Feedback/Evaluation, OpenTelemetry) replaces Langfuse — all native surfaces read from the same SQLite DB that Part 1 pseudonymizes.

**Preconditions:**
- IITR uses Keycloak SSO (`sso.iitr.de`) for OpenWebUI authentication — real PII enters through OIDC tokens
- OpenWebUI (v0.8.10 staging, v0.6.17 prod) is deployed as an unmodified upstream container — PII enters at OIDC login and is written to SQLite before any application code can intercept
- Native OpenWebUI observability (Analytics, Feedback, OTEL) reads from the same SQLite DB that Part 1 pseudonymizes — no separate PII surface once Langfuse is removed (Part 2)
- Three pseudonymization formats exist across the system: hex-encoded (old `protection.py`, one record in prod DB), `@anon.de` (simplified script, never ran on prod), and `@pseudonym.local` (one record in prod DB). Standardizing to a single format going forward (see Part 1)
- GDPR compliance requires pseudonymization of non-admin user data before broader rollout

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | All non-admin OIDC user PII is pseudonymized in OpenWebUI's SQLite DB on IITR-PROD, with scheduled re-pseudonymization for new OIDC users |
| **Success** | (1) Existing unhashed user records are batch-cleaned in SQLite — both old hex-encoded and new plaintext records detected and pseudonymized. (2) GHA scheduled workflow re-runs daily, catching new OIDC users. (3) Admin/developer users retain real identities for operational visibility |
| **Done test** | A test OIDC user logs into OpenWebUI on IITR-PROD → after next scheduled batch run, their name/email appears as `anon_{hash8}@pseudonym.local` in the SQLite `user` and `auth` tables → whitelisted admin accounts remain identifiable → native Analytics/Feedback show pseudonymized user activity |

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
- Script is idempotent: guard clause skips already-pseudonymized records by detecting non-real email patterns (`@pseudonym.local`, `@anon.de`, all-hex). **Standard format going forward: `anon_{sha256_first8}@pseudonym.local`** — `.local` is an IANA-reserved TLD (RFC 6762) that cannot route to a real mailbox, unlike `@anon.de` which is a live domain. The `anon_` prefix and `@pseudonym.local` domain make pseudonymized records instantly identifiable in admin UIs. 8 hex chars (~4 billion combinations) provide sufficient uniqueness for <100 users. Names use the same hash: `Anon_{sha256_first8}`. Legacy formats (hex-encoded, `@anon.de`) are detected and skipped — they cannot be migrated because the original email is lost

**1c. Old code cleanup:**
- Remove `masterfragen/src/utils/protection.py` (227 lines, not imported anywhere, dead code)
- Remove `masterfragen/package/README.md` (documents the deprecated complex system)
- Remove `masterfragen/app/` directory (Flask proxy — replaced by pipeline filters, dead code since PR #30)
- Remove old per-env compose files (`docker-compose.dev.yml`, `docker-compose.staging.yml`, `docker-compose.prod.yml` under `masterfragen/`) — superseded by root compose with CCI #646 infra/app split
- Keep `test_prod_pseudonymize.py` as a dry-run validation tool

**1d. Schema compatibility and timing gap:**
- Prod runs OpenWebUI 0.6.17, staging runs 0.8.10. The `user` (name, email, oauth_sub) and `auth` (email) table schemas are compatible across versions — both use VARCHAR fields that the script hashes. However, 0.8.10 may introduce additional PII-carrying fields or tables (e.g., `feedback.data` JSON, `message.content`). **Prerequisite:** audit the 0.8.10 schema on staging before production deployment — verify no new PII columns were added to `user`/`auth` and that `feedback`/`message` tables reference users by UUID (not by name/email).
- **Timing gap:** Between a new OIDC user's first login and the next scheduled batch run (~24h max), real PII exists in the SQLite DB and is visible in native OpenWebUI Analytics/Feedback. For IITR's current user base (handful of known OIDC users, most whitelisted), this window is acceptable. If the user base grows significantly, reduce the cron interval or add an OpenWebUI startup hook.

**1e. OIDC safety and reversibility:**
- Pseudonymizing the email field does not break OIDC re-login. OpenWebUI matches returning users by `oauth_sub` (primary lookup), not by email — 61/63 prod users have `oauth_sub` set as `oidc@<keycloak-uuid>`. Email is only used as a fallback if `OAUTH_MERGE_ACCOUNTS_BY_EMAIL` is enabled and `oauth_sub` lookup returns nothing. The `@pseudonym.local` format passes OpenWebUI's email validation regex (`[^@]+@[^@]+\.[^@]+`)
- SHA-256 hashing is one-way by design — pseudonymized records cannot be reversed to recover the original email. However, with <100 users, re-identification is trivial: hash every email in Keycloak's user database and match against pseudonymized values. Keycloak itself serves as the "separate information" that GDPR pseudonymization requires — no additional lookup table is needed at this scale

### Part 2: Langfuse Stack Removal

The Langfuse stack (6 services running on staging/prod) is being removed entirely as part of this scope. This eliminates the secondary PII surface (real user emails in trace metadata) rather than patching it with trace-level hashing — removal is cleaner than defensive coverage for a stack that would be deleted anyway.

**What gets removed:**
- Inline Langfuse code from the 3 pipeline filters (Navigation, Masterfragen, Court Judgments) — the `_init_langfuse()` method and inlet Langfuse spans in each filter (~100-135 LOC total)
- `masterfragen/app/util/langfuse_utils.py` — data masking helper (63 LOC), no longer needed once no code calls Langfuse
- The Langfuse stack from `docker-compose.infra.yml` — 6 services (langfuse-web, langfuse-worker, langfuse-postgres, langfuse-clickhouse, langfuse-minio, langfuse-redis) and their ~154-line config block
- Langfuse environment variables (`LANGFUSE_HOST`, `LANGFUSE_PUBLIC_KEY`, `LANGFUSE_SECRET_KEY`, `LANGFUSE_S3_*`) from `.env` files and compose passthrough
- Dead `CJ_LANGFUSE_*` env vars (provisioned but never read — carried forward as cleanup)
- `simple_app.py` Langfuse code is already removed as part of Part 1c (Flask proxy deletion) — not duplicated here

**What replaces it:** Native OpenWebUI observability — Analytics dashboard (usage metrics), Feedback modal with text annotations (101 entries already on staging from Stellmacher), and OpenTelemetry (4 env vars, exports to Grafana). All three read from the same SQLite DB that Part 1 pseudonymizes. The observability architecture itself is owned by #1093 — this Part covers only the removal, not the replacement design.

**Parallel to Part 1:** Langfuse removal and the batch pseudonymizer share no files and no ordering constraint — they can ship as parallel implementation sub-issues under #1100. Removing Langfuse does not require the batch pseudonymizer to land first, and vice versa.

**Historical traces:** Existing Langfuse traces (staging-only) contain unhashed PII but are discarded with the stack. Retroactive cleanup is unnecessary — the ClickHouse/Postgres volumes are dropped with removal.

### Part 3: Whitelist Management — Environment-Variable-Driven

The batch script needs a configurable whitelist of protected users. Currently hardcoded in `pseudonymize_simple.py` — this needs to become environment-driven.

**Design:**
- Single env var `PROTECTED_EMAILS` — comma-separated list of email addresses that should NOT be pseudonymized
- Read by the batch script (Part 1) and pipeline filter trace hashing (Part 2) at each run
- Defined in `.env` (gitignored, per CCI #646 convention — single `.env` per environment, scoped by `COMPOSE_PROJECT_NAME`). `PROTECTED_EMAILS` is configuration, not a secret, but varies per environment (staging whitelist differs from prod)

**Current known protected users:**
- `stellmacher@iitr.de` — Marvin Stellmacher (IITR technical contact)
- `skraska@iitr.de` — Sebastian Kraska (IITR)
- `marius.santiago.wilsch@gmail.com` — Marius Wilsch
- `fabian.doerringer@tngtech.com` — IITR Admin
- `test@test.de`, `admin@admin.de` — test accounts

**Removed:** Roman Rolnik (`holorolnik@gmail.com`) — terminated, no longer active. Still present in prod DB with plaintext data and in the batch script's hardcoded whitelist — both must be cleaned.

**Undefined:** Whitelist pending final confirmation from Marius. David's email TBD — confirm whether to add before production deployment.

### Part 4: Deployment & Verification

Deployment follows CCI #646 conventions: GHA scheduled workflow for batch operations, `deploy` SSH user for server access.

**Batch script deployment:**
- GHA scheduled workflow (`.github/workflows/pseudonymize.yml`) runs daily at 03:00 UTC
- SSHs as `deploy` user → runs `pseudonymize_simple.py` against the OpenWebUI container → logs to GHA
- Backup stored in `/home/shared/backups/webui_{env}_backup_{timestamp}.db` on host (bind-mounted volume). Backups retained for 30 days — the GHA workflow includes a cleanup step: `find /home/shared/backups/ -name "webui_*_backup_*.db" -mtime +30 -delete`

**Rollback:**
- Restore from backup file — `docker cp /home/shared/backups/{backup}.db container:/app/backend/data/webui.db` + container restart (via GHA as `deploy` user)

**Verification prerequisite:** Marius has copied the prod database to staging (`masterfragen/data/webui-prod.db`, committed in `a9531e4`). Staging runs OpenWebUI 0.8.10 — run the adapted script against the staging DB first to verify schema compatibility (see Part 1d) before production deployment.

---

## Source

- **Code (current):** [iitr-platform/staging](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/staging) — mono-repo with batch script and CCI #646 compose split
- **Batch script:** `masterfragen/package/pseudonymize_simple.py`
- **Prod DB snapshot:** `masterfragen/data/webui-prod.db` (OpenWebUI 0.6.17, 63 users, commit `a9531e4`)
- **Deploy convention:** [CCI #646 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-runtime-state-design)
- **Deploy infra:** [deploy-action](https://github.com/WILSCH-AI-SERVICES/deploy-action) — shared GHA composite
- **Langfuse decision:** [#1297](https://github.com/DaveX2001/deliverable-tracking/issues/1297) (closed) — Langfuse stripped, native OpenWebUI observability replaces it
- **Issue:** [#1100](https://github.com/DaveX2001/deliverable-tracking/issues/1100)
- **Epic:** [#959](https://github.com/DaveX2001/deliverable-tracking/issues/959) — IITR Contract Extension & Stabilization
- **Session:** `75e3c02c-7e7a-406d-bbc4-e3798445e438` (2026-03-29 extraction pass)
- **Session:** `fc34fe77-e32c-40e2-a23d-211273428f1f` (2026-03-30 SA feedback resolution)
- **Session:** `6bb689e1-1e1e-493d-8983-ef6f3eb45451` (2026-04-01 fresh design pass — scope expansion to all 3 filters)
- **Session:** `e740877e-a9c8-45df-b7e6-26895b6d7723` (2026-04-03 SA feedback pass — Langfuse stripped, scope narrowed to SQLite DB, Part 2 dissolved, CCI #646 alignment)
- **Session:** `43c3a4bb-ae78-4a83-8bf5-993d4359878d` (2026-04-04 SA feedback pass — Langfuse reality correction, format standardization to @pseudonym.local, Part 2 restored, backup retention, OIDC/reversibility)
- **Session:** `f0cf71cd-4fca-493d-ad57-1500bbc39e54` (2026-04-05 SA feedback pass — Apr 5 feedback applied, Part 2 rewritten as Langfuse Stack Removal, full stack removal scope, parallel to Part 1)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

