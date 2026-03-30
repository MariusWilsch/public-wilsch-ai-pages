---
publish: true
---

# PII Pseudonymizer Design — OpenWebUI User Protection
[[project-iitr-pii-pseudonymizer-design]]

Design for pseudonymizing user PII across OpenWebUI's SQLite database and Langfuse observability traces on IITR-PROD, covering both batch cleanup of existing records and real-time protection for new OIDC users.

---

## Problem Statement

OpenWebUI on IITR-PROD stores real user identities (names, emails) from Keycloak OIDC authentication in two locations:

1. **SQLite DB** — `user` and `auth` tables contain plaintext names and emails for every authenticated user
2. **Langfuse traces** — the Flask proxy passes raw `X-OpenWebUI-User-Email` headers as `user_id` to Langfuse, creating traceable PII in observability data

A simplified pseudonymization script exists (`masterfragen/package/pseudonymize_simple.py`, 104 lines) but is hardcoded to the dev environment and has never been executed on production. An older, complex protection system (`masterfragen/src/utils/protection.py`) coexists in the repo but is not imported by the simplified script. Neither system is active.

**Preconditions:**
- IITR uses Keycloak SSO (`sso.iitr.de`) for OpenWebUI authentication — real PII enters through OIDC tokens
- OpenWebUI is deployed as an unmodified upstream container — PII enters at OIDC login and is written to SQLite before the Flask proxy can intercept
- The masterfragen Flask proxy already extracts user identity headers on every request but passes them through unhashed
- GDPR compliance requires pseudonymization of non-admin user data before broader rollout

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | All non-admin OIDC user PII is pseudonymized across both surfaces (SQLite DB and Langfuse traces) on IITR-PROD, with automated protection for new users |
| **Success** | (1) Existing unhashed user records are batch-cleaned. (2) New OIDC logins produce pseudonymized identities in Langfuse traces from first request. (3) Admin/developer users retain their real identities for operational visibility |
| **Done test** | A test OIDC user logs into OpenWebUI on IITR-PROD → their name/email appears hashed in both the SQLite DB and Langfuse traces → whitelisted admin accounts remain identifiable |

---

## Approach

### Part 1: Batch Pseudonymizer — Environment-Aware Script

The existing `pseudonymize_simple.py` (104 lines) provides the core logic: copy SQLite DB from Docker container → SHA-256 hash non-whitelisted user names/emails → copy DB back. Three changes make it production-ready:

**1a. Environment parameterization:**
- Container name derived from `COMPOSE_PROJECT_NAME` env var: `${COMPOSE_PROJECT_NAME}-openwebui-1` (e.g., `iitr-staging-openwebui-1`, `iitr-prod-openwebui-1`)
- `PSEUDONYMIZE_ENV` env var (already exists in the script) selects the whitelist
- DB working path: `/tmp/webui_{env}.db` (transient) — backup path: `/home/shared/backups/` (persistent, survives reboots)

**1b. Scheduled execution:**
- Cron inside the proxy container (pattern already proven — `Dockerfile.prod` runs `simple_mail.py` daily at 22:00 via cron)
- Add pseudonymization as a second cron job, e.g., daily at 03:00
- Script is idempotent: already-hashed emails (`*@anon.de`) won't match the whitelist exclusion, but re-hashing produces a different hash — needs a guard clause to skip already-pseudonymized records (e.g., skip emails ending in `@anon.de`)

**1c. Old code cleanup:**
- Remove `masterfragen/src/utils/protection.py` (not imported by simplified script, dead code)
- Remove `masterfragen/package/README.md` documentation of the old complex system
- Keep `test_prod_pseudonymize.py` as a dry-run validation tool

### Part 2: Flask Proxy Pseudonymization — Langfuse Trace Protection

The Flask proxy (`masterfragen/app/simple_app.py`) extracts OpenWebUI user identity headers on every request and passes them to Langfuse as `user_id`. This is the real-time PII surface.

**Interception point:** In `simple_app.py`, where `X-OpenWebUI-User-Email` is read and passed to `langfuse.update_current_trace(user_id=...)`. Apply the same SHA-256 hash (`hash_pii()` function) before the Langfuse call.

**Whitelist check at proxy level:**
- If the email matches a whitelisted address → pass through unhashed (admin visibility in Langfuse dashboards)
- If not → hash before writing to trace
- Whitelist loaded from environment variable (see Part 3)

**Scope boundary:** This covers the masterfragen Flask proxy only. The navigation and court-judgments pipeline filters (`inlet()` functions) have the same pattern (`user_id=user_name` to Langfuse) but are out of scope for #1100 — they run on the root compose stack (OpenWebUI 0.8.10), not the masterfragen instance.

### Part 3: Whitelist Management — Environment-Variable-Driven

Both the batch script and the Flask proxy need a shared whitelist of protected users. Currently the whitelist is hardcoded in `pseudonymize_simple.py` — this needs to become configurable.

**Design:**
- Single env var `PROTECTED_EMAILS` — comma-separated list of email addresses that should NOT be pseudonymized
- Read by both the batch script and the Flask proxy
- Per-environment `.env` files already exist (`.env.dev`, `.env.staging`, `.env.prod`) — add `PROTECTED_EMAILS` to each
- Shared `hash_pii()` function extracted to a utility module importable by both the batch script and `simple_app.py`

**Current known protected users:**
- `stellmacher@iitr.de` — Marvin Stellmacher (IITR technical contact)
- `skraska@iitr.de` — Sebastian Kraska (IITR)
- `marius.santiago.wilsch@gmail.com` — Marius Wilsch
- `fabian.doerringer@tngtech.com` — IITR Admin
- `test@test.de`, `admin@admin.de` — test accounts

**Undefined:** Whitelist pending update from Marius (2026-03-30). Current list is a snapshot — final protected user set to be confirmed before production deployment. Roman Rolnik already removed (no longer active).

### Part 4: Deployment & Rollback

Deployment follows the established `deploy-action` GHA pattern (SSH-based, two-phase compose).

**Batch script deployment:**
- Add `pseudonymize_simple.py` to the proxy container image (already in the repo, just needs Dockerfile inclusion)
- Add cron entry in `Dockerfile.prod` alongside existing `simple_mail.py` cron
- Backup stored in `/home/shared/backups/webui_{env}_backup_{timestamp}.db` on the host (bind-mounted volume)

**Flask proxy deployment:**
- Changes to `simple_app.py` deploy automatically via the existing staging → prod workflow
- Push to `staging` branch → deploy-action runs two-phase deploy → verify on staging → merge to prod

**Rollback:**
- Batch: restore from backup file — `docker cp /home/shared/backups/{backup}.db container:/app/backend/data/webui.db` + container restart
- Proxy: revert commit + redeploy (standard git workflow)

**Verification prerequisite:** Staging currently has no non-pseudonymized OIDC test users. Marius to copy prod database to staging so the batch script can be tested against real (unhashed) data before production execution.

---

## Source

- **Code (current):** [iitr-platform/staging/masterfragen](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/staging/masterfragen) — mono-repo containing Flask proxy, batch script, and Docker config
- **Code (legacy):** [IITR__IITR-RAG-Masterfragen](https://github.com/WILSCH-AI-SERVICES/IITR__IITR-RAG-Masterfragen) — old standalone repo, superseded by mono-repo migration (#1191)
- **Work log:** `masterfragen/docs/work-log/work-log-2025-09-08-...-pseudonymization-simplification.md` — documents the Sep 2025 simplification from 2,085 to 104 lines
- **Deploy infra:** [deploy-action](https://github.com/WILSCH-AI-SERVICES/deploy-action) — shared GHA composite (#1291)
- **Compose redesign:** #1281 — infra/app convention split
- **Issue:** [#1100](https://github.com/DaveX2001/deliverable-tracking/issues/1100)
- **Epic:** [#959](https://github.com/DaveX2001/deliverable-tracking/issues/959) — IITR Contract Extension & Stabilization
- **Session:** `75e3c02c-7e7a-406d-bbc4-e3798445e438` (2026-03-29 extraction pass)
- **Session:** `fc34fe77-e32c-40e2-a23d-211273428f1f` (2026-03-30 SA feedback resolution — F1: closed-source→upstream, F2: Makefile→COMPOSE_PROJECT_NAME, F3: whitelist Undefined)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

