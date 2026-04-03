---
publish: true
---

# PII Pseudonymizer Design — OpenWebUI User Protection
[[project-iitr-pii-pseudonymizer-design]]

Design for pseudonymizing user PII in OpenWebUI's SQLite database on IITR-PROD. Batch cleanup of existing records plus scheduled re-runs for new OIDC users. Observability surfaces (native OpenWebUI Analytics, Feedback, OTEL) read from the same DB — pseudonymizing the source covers all downstream consumers.

---

## Problem Statement

OpenWebUI on IITR-PROD stores real user identities (names, emails) from Keycloak OIDC authentication in its SQLite database — `user` and `auth` tables contain plaintext names and emails for every authenticated user. The prod DB (v0.6.17, 63 users) contains unprotected PII for non-admin OIDC users (e.g., Lisa Milbredt, Ralf Zlamal) alongside whitelisted admin accounts.

A simplified pseudonymization script exists (`masterfragen/package/pseudonymize_simple.py`, 104 lines) but is hardcoded to the dev environment and has never been executed on production. An older protection system (`masterfragen/src/utils/protection.py`, 227 lines) coexists but is dead code — not imported anywhere. Evidence of both systems in the DB: one user record has hex-encoded name/email from the old system, while no `@anon.de` format records exist (simplified script never ran).

**Observability context (#1297, closed 2026-04-02):** Custom Langfuse pipeline tracing has been stripped — inline Langfuse code removed from all three filters, Langfuse stack (6 services) scheduled for removal from `docker-compose.infra.yml`. Native OpenWebUI observability (Analytics dashboard, Feedback/Evaluation, OpenTelemetry) replaces it. All native surfaces read from the same SQLite DB — pseudonymizing the DB covers observability automatically.

**Preconditions:**
- IITR uses Keycloak SSO (`sso.iitr.de`) for OpenWebUI authentication — real PII enters through OIDC tokens
- OpenWebUI (v0.8.10 staging, v0.6.17 prod) is deployed as an unmodified upstream container — PII enters at OIDC login and is written to SQLite before any application code can intercept
- Native OpenWebUI observability (Analytics, Feedback, OTEL) reads from the same SQLite DB — no separate PII surface
- Two incompatible pseudonymization formats exist in the DB: hex-encoded (old `protection.py`) and `@anon.de` (simplified script, never ran on prod)
- GDPR compliance requires pseudonymization of non-admin user data before broader rollout

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | All non-admin OIDC user PII is pseudonymized in OpenWebUI's SQLite DB on IITR-PROD, with scheduled re-pseudonymization for new OIDC users |
| **Success** | (1) Existing unhashed user records are batch-cleaned in SQLite — both old hex-encoded and new plaintext records detected and pseudonymized. (2) GHA scheduled workflow re-runs daily, catching new OIDC users. (3) Admin/developer users retain real identities for operational visibility |
| **Done test** | A test OIDC user logs into OpenWebUI on IITR-PROD → after next scheduled batch run, their name/email appears as `{hash}@anon.de` in the SQLite `user` and `auth` tables → whitelisted admin accounts remain identifiable → native Analytics/Feedback show pseudonymized user activity |

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
- Script is idempotent: guard clause skips already-pseudonymized records in THREE formats — `@anon.de` (simplified script), hex-encoded names/emails (old `protection.py` system, detected by all-hex pattern), and `@pseudonym.local` (if present). The prod DB contains at least one hex-encoded record from the old system

**1c. Old code cleanup:**
- Remove `masterfragen/src/utils/protection.py` (227 lines, not imported anywhere, dead code)
- Remove `masterfragen/package/README.md` (documents the deprecated complex system)
- Remove `masterfragen/app/` directory (Flask proxy — replaced by pipeline filters, dead code since PR #30)
- Remove old per-env compose files (`docker-compose.dev.yml`, `docker-compose.staging.yml`, `docker-compose.prod.yml` under `masterfragen/`) — superseded by root compose with CCI #646 infra/app split
- Keep `test_prod_pseudonymize.py` as a dry-run validation tool

**1d. Schema compatibility and timing gap:**
- Prod runs OpenWebUI 0.6.17, staging runs 0.8.10. The `user` (name, email, oauth_sub) and `auth` (email) table schemas are compatible across versions — both use VARCHAR fields that the script hashes. However, 0.8.10 may introduce additional PII-carrying fields or tables (e.g., `feedback.data` JSON, `message.content`). **Prerequisite:** audit the 0.8.10 schema on staging before production deployment — verify no new PII columns were added to `user`/`auth` and that `feedback`/`message` tables reference users by UUID (not by name/email).
- **Timing gap:** Between a new OIDC user's first login and the next scheduled batch run (~24h max), real PII exists in the SQLite DB and is visible in native OpenWebUI Analytics/Feedback. For IITR's current user base (handful of known OIDC users, most whitelisted), this window is acceptable. If the user base grows significantly, reduce the cron interval or add an OpenWebUI startup hook.

### Part 2: Whitelist Management — Environment-Variable-Driven

The batch script needs a configurable whitelist of protected users. Currently hardcoded in `pseudonymize_simple.py` — this needs to become environment-driven.

**Design:**
- Single env var `PROTECTED_EMAILS` — comma-separated list of email addresses that should NOT be pseudonymized
- Read by the batch script (Part 1) at each scheduled run
- Defined in `.env` (gitignored, per CCI #646 convention — single `.env` per environment, scoped by `COMPOSE_PROJECT_NAME`). `PROTECTED_EMAILS` is configuration, not a secret, but varies per environment (staging whitelist differs from prod)

**Current known protected users:**
- `stellmacher@iitr.de` — Marvin Stellmacher (IITR technical contact)
- `skraska@iitr.de` — Sebastian Kraska (IITR)
- `marius.santiago.wilsch@gmail.com` — Marius Wilsch
- `fabian.doerringer@tngtech.com` — IITR Admin
- `test@test.de`, `admin@admin.de` — test accounts

**Removed:** Roman Rolnik (`holorolnik@gmail.com`) — terminated, no longer active. Still present in prod DB with plaintext data and in the batch script's hardcoded whitelist — both must be cleaned.

**Undefined:** Whitelist pending final confirmation from Marius. David's email TBD — confirm whether to add before production deployment.

### Part 3: Deployment & Verification

Deployment follows CCI #646 conventions: GHA scheduled workflow for batch operations, `deploy` SSH user for server access.

**Batch script deployment:**
- GHA scheduled workflow (`.github/workflows/pseudonymize.yml`) runs daily at 03:00 UTC
- SSHs as `deploy` user → runs `pseudonymize_simple.py` against the OpenWebUI container → logs to GHA
- Backup stored in `/home/shared/backups/webui_{env}_backup_{timestamp}.db` on host (bind-mounted volume)

**Rollback:**
- Restore from backup file — `docker cp /home/shared/backups/{backup}.db container:/app/backend/data/webui.db` + container restart (via GHA as `deploy` user)

**Verification prerequisite:** Marius has copied the prod database to staging (`masterfragen/data/webui-prod.db`, committed in `a9531e4`). Staging runs OpenWebUI 0.8.10 — run the adapted script against the staging DB first to verify schema compatibility (see Part 1d) before production deployment.

**Note on historical Langfuse traces:** Prior Langfuse traces contain unhashed PII from usage before pseudonymization. Since Langfuse is being stripped (#1297, closed 2026-04-02) and existing traces are staging data only, retroactive cleanup is not worth the effort. If the official Langfuse pipeline filter is later enabled as a backup, it would start fresh — all new traces would reference pseudonymized user records from Part 1.

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

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

