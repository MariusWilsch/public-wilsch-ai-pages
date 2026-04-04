---
publish: true
---

# PII Pseudonymizer Design — OpenWebUI User Protection
[[project-iitr-pii-pseudonymizer-design]]

Design for pseudonymizing user PII across OpenWebUI's SQLite database and Langfuse pipeline traces on IITR-PROD. Batch cleanup of existing DB records, scheduled re-runs for new OIDC users, and trace-level hashing while Langfuse remains active.

---

## Problem Statement

OpenWebUI on IITR-PROD stores real user identities (names, emails) from Keycloak OIDC authentication in its SQLite database — `user` and `auth` tables contain plaintext names and emails for every authenticated user. The prod DB (v0.6.17, 63 users) contains unprotected PII for non-admin OIDC users (e.g., Lisa Milbredt, Ralf Zlamal) alongside whitelisted admin accounts.

A simplified pseudonymization script exists (`masterfragen/package/pseudonymize_simple.py`, 104 lines) but is hardcoded to the dev environment and has never been executed on production. An older protection system (`masterfragen/src/utils/protection.py`, 227 lines) coexists but is dead code — not imported anywhere. Evidence of both systems in the DB: one user record has hex-encoded name/email from the old system, while no `@anon.de` format records exist (simplified script never ran).

**Observability context (#1297, closed 2026-04-02):** Custom Langfuse pipeline tracing is scheduled for removal but has not been stripped yet — #1297 was closed but its implementation PR (#32) was not merged. All three pipeline filters still contain inline Langfuse code (~106 lines total) sending `user_id` to Langfuse traces, and the Langfuse stack (6 services) remains in `docker-compose.infra.yml`. Until Langfuse is stripped, pipeline filter traces are a live PII surface requiring pseudonymization (see Part 2). Native OpenWebUI observability (Analytics dashboard, Feedback/Evaluation, OpenTelemetry) is the intended replacement — all native surfaces read from the same SQLite DB, so pseudonymizing the DB covers native observability automatically.

**Preconditions:**
- IITR uses Keycloak SSO (`sso.iitr.de`) for OpenWebUI authentication — real PII enters through OIDC tokens
- OpenWebUI (v0.8.10 staging, v0.6.17 prod) is deployed as an unmodified upstream container — PII enters at OIDC login and is written to SQLite before any application code can intercept
- Native OpenWebUI observability (Analytics, Feedback, OTEL) reads from the same SQLite DB — no separate PII surface once Langfuse is stripped. While Langfuse remains, pipeline filter traces are an additional PII surface
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

### Part 2: Pipeline Filter Trace Pseudonymization — While Langfuse Remains

All three pipeline filters (Navigation, Masterfragen, Court Judgments) send `user_id` to Langfuse traces via their `_init_langfuse()` and inlet methods (~106 lines total across filters). While Langfuse remains on staging/prod, this is a live PII surface — real user emails appear in Langfuse trace metadata.

**Design:** Each filter's inlet applies the shared `hash_pii()` utility (from Part 1) to the user identifier before passing it to Langfuse trace creation. The same `PROTECTED_EMAILS` whitelist (Part 3) governs which users pass through unhashed for admin visibility in Langfuse dashboards.

**Scope note:** This part becomes unnecessary once Langfuse is fully stripped (#1297 implementation). The ~363 lines of Langfuse code (106 in filters, 175 in `docker-compose.infra.yml`, 63 in `langfuse_utils.py`, 8 env vars, 11 in `simple_app.py`) are a separate cleanup task from the pseudonymizer itself.

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

**Note on historical Langfuse traces:** Prior Langfuse traces contain unhashed PII from usage before pseudonymization. Since Langfuse stripping is pending (#1297 closed, PR #32 not merged) and existing traces are staging data only, retroactive cleanup is not worth the effort. If the official Langfuse pipeline filter is later enabled as a backup, it would start fresh — all new traces would reference pseudonymized user records from Part 1.

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

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

