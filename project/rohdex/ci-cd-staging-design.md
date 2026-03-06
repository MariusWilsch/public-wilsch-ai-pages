---
publish: true
---

# CI/CD & Staging Environment Design — Dokumentenverarbeitung
[[project-rohdex]]

Design for the CI/CD pipeline and staging environment of the email-based Dokumentenverarbeitung system running on RDX-APP-01.

---

## Problem Statement

The Dokumentenverarbeitung system runs on RDX-APP-01 as a single Docker container polling the IONOS email inbox (export-ki@rohdex.com). Code changes reach the server through ad-hoc rsync over VPN — no deploy script, no CI pipeline, no staging environment. There is no way to verify changes before they affect production email processing.

The system's email-driven architecture creates a specific isolation challenge: both staging and production would poll the same IMAP inbox. The first instance to poll consumes the email (moves it to Processed folder), preventing the other from seeing it. During migration (#1046), this was managed by manually stopping one server — not sustainable for ongoing development.

This design doc covers three areas: (1) how to isolate staging email from production, (2) how to deploy code given server constraints, and (3) how branches map to environments.

**Preconditions:**
- RDX-APP-01 is sole production (migration #1046 complete, old server stopped)
- No outbound GitHub access from RDX-APP-01 (Gmelch IT firewall)
- VPN-only access to server (OpenVPN → 192.168.44.11)
- IONOS email credentials are app-only — Wilsch AI cannot create new mailboxes
- Codebase: MariusWilsch/rohdex (default branch: staging)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Code changes are verified on a staging environment before reaching production email processing |
| **Success** | A developer can deploy to staging, verify document generation works, then promote to production — without risking production emails being consumed by staging |
| **Done test** | Can a developer follow this document end-to-end to: (1) deploy to staging, (2) test without affecting production, (3) promote to production? If yes → design is complete |

---

## Approach

### Part 1: Staging Email Isolation

The system polls IMAP and **moves** emails from INBOX to a Processed or Skipped folder after handling them. Two instances polling the same inbox creates a first-poller-wins race condition — whichever instance polls first consumes the email, hiding it from the other.

**Recommendation: Second IONOS mailbox for staging.**

A dedicated staging mailbox (e.g., `staging-export-ki@rohdex.com`) gives staging its own email pipeline. Production continues polling `export-ki@rohdex.com` undisturbed. The `WHICH_IMAP` / `IONOS_IMAP_EMAIL` env vars already support pointing each environment at a different mailbox — no code changes needed.

| Environment | IONOS Mailbox | Port |
|-------------|--------------|------|
| Production  | `export-ki@rohdex.com` | 9000 |
| Staging     | `staging-export-ki@rohdex.com` (TBD) | 9001 |

**Interim (before mailbox exists):** The internal endpoint `POST /internal/process-email` accepts attachments directly, enabling document generation testing without email polling. Staging can run with polling disabled (requires adding a `POLLING_ENABLED` env var to skip the asyncio polling task in `main.py` lifespan).

**Undefined:** Second IONOS mailbox creation — who requests it, through which channel, and timeline. See [Meeting Agenda: Staging Email Mailbox](ci-cd-staging-agenda).

### Part 2: Deployment Mechanism

RDX-APP-01 has no outbound GitHub access (Gmelch IT firewall). Code cannot be pulled from GitHub on the server. Two deployment paths exist depending on whether this constraint changes.

#### Path A: rsync-push (current, works today)

Developer pushes code to the server over VPN:

```
1. VPN connect (OpenVPN → 192.168.44.11)
2. rsync code to RDX-APP-01:
   rsync -avz --exclude='.git' --exclude='__pycache__' ./ wilsch@192.168.44.11:~/rohdex/
3. SSH into server:
   ssh RDX-APP-01
4. Build and restart:
   cd ~/rohdex && make deploy
```

The Makefile handles on-server orchestration: `make deploy` (build image + restart container), `make up`, `make down`, `make restart`, `make logs`, `make status`.

**Tradeoffs:** Simple, no external dependencies, works today. No audit trail beyond git history — deploy is not linked to a specific commit unless manually verified via `/health` endpoint (returns `git_sha`).

#### Path B: git-pull (requires GitHub access)

If Gmelch IT opens outbound access to `github.com` from RDX-APP-01:

```
1. SSH into server (via VPN)
2. git pull origin staging
3. make deploy
```

Optionally, GitHub Actions could automate this: on merge to `staging` or `main`, a workflow connects to the VPN, SSHs into the server, and runs `git pull && make deploy`. This requires storing VPN config + SSH keys in GitHub Secrets.

**Tradeoffs:** Cleaner workflow, deploy tied to commits, CI/CD possible. Requires Gmelch IT to open firewall (external dependency) and VPN credentials in GitHub (security surface).

**Decision point for SA:** Path A is recommended as the starting point. Path B is a future improvement if Marius requests GitHub access from Gmelch IT.

#### Rollback

If a deploy breaks production:
1. `git checkout` the previous working commit
2. rsync (or git pull) + `make deploy`
3. Verify via `/health` endpoint (`git_sha` matches expected commit)

Future improvement: tag Docker images before deploying (`docker tag rohdex-backend:latest rohdex-backend:$(git rev-parse --short HEAD)`) to enable instant rollback without rebuild.

### Part 3: Branch-to-Environment Mapping

| Branch | Environment | Mailbox | Port | Deploy Trigger |
|--------|------------|---------|------|----------------|
| `main` | Production | `export-ki@rohdex.com` | 9000 | Manual (rsync + make deploy) |
| `staging` | Staging | staging mailbox (TBD) | 9001 | Manual (rsync + make deploy) |
| `issue-*` | Local dev only | N/A | N/A | N/A |

**`staging` is the default branch** (set during #1046 migration). All feature branches are created from and merged back to `staging`.

#### Merge and Deploy Flow

```
feature branch (issue-XXX)
  → PR to staging (Marius reviews)
  → merge → manual deploy to staging
  → verify on staging (send test email, check generated documents)
  → PR to main
  → merge → manual deploy to production
```

**Review gate:** All PRs require Marius (SA) review before merge. This is the existing pattern from #1046 (PR #7 to staging).

**Deploy is always manual.** After merge, the developer runs the deploy sequence (rsync + ssh + make deploy) via Claude Code terminal. No auto-deploy on merge — consistent with Path A deployment.

#### Env Var Differences

Staging and production share the same `.env` structure. Only these values differ:

| Env Var | Production | Staging |
|---------|-----------|---------|
| `IONOS_IMAP_EMAIL` | `export-ki@rohdex.com` | staging mailbox |
| `IONOS_IMAP_PASSWORD` | prod credentials | staging credentials |
| `IONOS_SMTP_EMAIL` | `export-ki@rohdex.com` | staging mailbox |
| `IONOS_SMTP_PASSWORD` | prod credentials | staging credentials |
| Docker port mapping | `9000:9000` | `9001:9000` |

Both environments run on RDX-APP-01 as separate Docker containers with separate `.env` files and separate docker-compose configurations.

---

## Source

- **Issue:** [#1067](https://github.com/DaveX2001/deliverable-tracking/issues/1067)
- **Epic:** [#909 Dokumentenverarbeitung SLA & Wartung](https://github.com/DaveX2001/deliverable-tracking/issues/909)
- **Migration:** [#1046](https://github.com/DaveX2001/deliverable-tracking/issues/1046) (closed — deployment patterns established here)
- **Design Doc:** [hosting-anforderungen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen) (system architecture reference)
- **Codebase:** [MariusWilsch/rohdex](https://github.com/MariusWilsch/rohdex) (default branch: staging)
- **Rubber-duck session:** [9d1941ec](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/9d1941ec-6175-4d1e-9df0-2a0b1301d055.jsonl)
- **This session:** [15641742](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/15641742-b196-41d6-85d2-13f45111ff65.jsonl)
