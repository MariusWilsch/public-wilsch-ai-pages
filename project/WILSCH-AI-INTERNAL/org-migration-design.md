---
publish: true
---

# Org & Server Migration — WILSCH-AI-SERVICES Consolidation
[[client-00_wilsch-ai-internal]]

Migration plan for transferring all active repositories to the WILSCH-AI-SERVICES GitHub organization and consolidating server-side project checkouts under `/home/deploy/projects/`. Covers repo transfers, server-side migration, convention adoption per project, and reference updates.

---

## Problem Statement

Repositories and server deployments are scattered across three GitHub accounts and four server directories. This fragmentation blocks the deployment convention (CCI #646) from scaling beyond its first two adopters.

**Current fragmentation:**
- **GitHub:** Active repos split across `MariusWilsch/` (50 repos), `DaveX2001/` (7 repos), and `WILSCH-AI-SERVICES/` (8 repos). Client repos under personal accounts lack org-level branch protection and require per-repo App installation.
- **Server:** Project checkouts in `/home/marius/projects/billable/`, `/home/shared/projects/`, `/home/shared/`, `/home/david/projects/`, and `/home/david/` — each with different ownership (marius, david, root). Only `/home/deploy/projects/archibus-bulk-import` follows the convention.
- **Containers:** 29 running containers, most with hardcoded ports and manual deployment. Only `archibus-bulk-import` and `issue-1190` (preview) use the convention's auto-assigned ports and health checks.

**What this IS:** A consolidation plan — moving repos to one org, checkouts to one path, and classifying each project's convention adoption path.

**What this ISN'T:** A redesign of the deployment convention itself (that's CCI #646). This doc assumes the convention is proven and focuses on migration.

**Preconditions:**
- The deployment convention (infra/app split, deploy-action, push=preview) is validated — archibus-bulk-import passed 7/7 ACs (#1251), iitr-platform adopted the compose split (#1281)
- `deploy` user exists on server (uid=1005, docker + dev-team groups)
- WILSCH-AI-SERVICES org is on Team plan (2 members, org-level rulesets available)
- `deploy-action` reusable workflow exists and is tested

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | All active client and internal repos live under WILSCH-AI-SERVICES. All server-side project checkouts live under `/home/deploy/projects/` with `deploy:dev-team` ownership. Each project classified for convention adoption. |
| **Success** | A Developer can clone any active project from `WILSCH-AI-SERVICES/`, deploy via `deploy-action`, and get push=preview — without per-repo setup. Org-level branch protection and GitHub App cover all repos uniformly. |
| **Done test** | Can every active project be found under one org, deployed from one server path, and protected by one ruleset? If yes → migration complete. |

---

## Approach

### Part 1: Current State Inventory

#### GitHub — Repos by Account

**WILSCH-AI-SERVICES** (8 repos, already in org):
- `iitr-platform` — IITR chat + RAG (convention-compliant)
- `archibus-bulk-import` — Archibus bulk data entry (convention-compliant, proven)
- `deploy-action` — Reusable GHA deployment workflow (public)
- `wilsch-ai-team-plugin` — Claude Code team plugin
- `IITR__IITR-RAG-Navigation`, `IITR__IITR-RAG-Court-Judgments`, `IITR__IITR-RAG-Masterfragen`, `IITR__iitr-infrastructure` — IITR RAG subprojects

**MariusWilsch** (active client repos to transfer):
- `uwi__invoice-agent` — UWI invoice processing
- `Call2Tanss` — Call2Tanss telephony integration
- `ARCHIBUS__archibus-poc` — Legacy Archibus (LibreChat-based, being replaced by archibus-bulk-import)
- `rohdex` — Rohdex backend (no convention adoption needed — transfer only)

**MariusWilsch** (internal/tooling — evaluate):
- `00_WILSCH-AI-INTERNAL__soloforce` — Marius's working directory
- `public-wilsch-ai-pages` — Hippocampus publishing (canonical copy)
- `claude-code-conversation-store` — Session audit trail

**DaveX2001** (to transfer):
- `deliverable-tracking` — Issue tracker (1300+ issues, highest-risk transfer)
- `claude-code-improvements` — System improvement tracker
- `public-wilsch-ai-pages` — Hippocampus mirror (non-canonical, MariusWilsch is source)

**Out of scope:** klimafolgenschutz-website, wilsch-ai-site, vibe-marketing, eberle-plugins, moltbot — personal/non-client repos stay in personal accounts.

#### Server — Project Checkouts (as of 2026-03-29)

| Project | Path | Owner | Remote | Convention? |
|---------|------|-------|--------|-------------|
| archibus-bulk-import | `/home/deploy/projects/` | deploy:dev-team | WILSCH-AI-SERVICES ✅ | ✅ Yes |
| archibus-bulk-import (dup) | `/home/shared/projects/` | marius:dev-team | WILSCH-AI-SERVICES | Legacy copy |
| archibus_full-stack | `/home/shared/projects/` | marius:dev-team | MariusWilsch/ARCHIBUS__archibus-poc | ❌ Legacy |
| archibus-deploy | `/home/marius/projects/billable/` | marius | No remote (LibreChat upstream clone) | ❌ Legacy |
| call2tanss_full-stack | `/home/marius/projects/billable/` | marius | MariusWilsch/Call2Tanss | ❌ No |
| invoice-agent_backend | `/home/marius/projects/billable/` | marius | MariusWilsch/uwi__invoice-agent | ❌ No |
| rohdex | `/home/shared/` | ? | MariusWilsch/rohdex | ❌ No |

#### Server — Running Containers (29 total, grouped)

**Convention-compliant (auto-ports, healthchecks):**
- `archibus-bulk-import-*` (3 containers) — staging
- `issue-1190-*` (3 containers) — preview deployment

**Legacy Archibus (hardcoded ports):**
- `LibreChat-staging` (:3081), `LibreChat-dev` (:3080) — old Archibus chat UI
- `archibus_fastmcp_staging` (:8001) — old Archibus MCP
- `chat-mongodb-*`, `rag_api-*`, `vectordb-*`, `chat-meilisearch-*` — supporting services

**Client projects (hardcoded ports):**
- `invoice-production-backend` (:8081) — UWI production
- `rohdex-staging` (:9000)
- `call2tanss` (:5000), `call2tanss-dev` (:5001), `call2tanss-staging` (:5002)

**Out of scope (stay as-is):**
- `klimafolgenschutz-cms` (:3100), `moltbot-*` (:18789-90) — personal projects
- `docker-socket-proxy`, `buildkit`, `buildx_buildkit_*`, `metamcp-pg` — infrastructure

### Part 2: Per-Project Migration Classification

Each project gets one of three migration paths:

| Path | What it means | Example |
|------|--------------|---------|
| **Transfer only** | Move repo to org + checkout to `/home/deploy`. No compose changes. | rohdex |
| **Transfer + adopt** | Move repo + restructure to infra/app split + wire deploy-action. | invoice-agent, call2tanss |
| **Already done** | Already in org and convention-compliant. | archibus-bulk-import, iitr-platform |

#### Classification Table

| Project | GitHub Transfer | Server Migration | Convention Adopt | Priority | Notes |
|---------|----------------|-----------------|-----------------|----------|-------|
| **archibus-bulk-import** | ✅ Done | ✅ Done | ✅ Done | — | First adopter, proven |
| **iitr-platform** | ✅ Done | On IITR-STAGING server | ✅ Done | — | Separate server (136.243.71.58) |
| **invoice-agent** | MariusWilsch → org | `/home/marius/...` → `/home/deploy/` | Adopt (has backend, needs compose split) | High | Marius: "block invoice-agent davon" |
| **call2tanss** | MariusWilsch → org | `/home/marius/...` → `/home/deploy/` | Adopt (3 instances: prod/dev/staging) | Medium | 3 running instances with hardcoded ports |
| **ARCHIBUS__archibus-poc** | MariusWilsch → org | `/home/shared/` → cleanup | ❌ Skip (legacy, being replaced) | Low | Legacy — archibus-bulk-import replaces it |
| **rohdex** | MariusWilsch → org | `/home/shared/` → `/home/deploy/` | Transfer only | Low | No convention adoption needed |
| **deliverable-tracking** | DaveX2001 → org | No server component | N/A | Last | 1300+ issues, highest redirect risk |
| **claude-code-improvements** | DaveX2001 → org | No server component | N/A | Last | Paired with deliverable-tracking |
| **public-wilsch-ai-pages** | MariusWilsch → org | No server component | N/A | Medium | Canonical copy (DaveX2001 copy is mirror) |
| **claude-code-conversation-store** | MariusWilsch → org | No server component | N/A | Low | Audit trail repo |
| **00_WILSCH-AI-INTERNAL__soloforce** | MariusWilsch → org | No server component | N/A | Low | Marius's working dir |

#### Legacy Cleanup (post-migration)

After convention-compliant replacements are running:
- **Remove** `/home/shared/projects/archibus-bulk-import` (duplicate of `/home/deploy/`)
- **Remove** `/home/shared/projects/archibus_full-stack` (replaced by archibus-bulk-import)
- **Remove** `/home/marius/projects/billable/archibus-deploy` (dead LibreChat clone, no remote)
- **Stop** old Archibus containers: `LibreChat-staging`, `LibreChat-dev`, `archibus_fastmcp_staging` + supporting services
- **Remove** old checkouts from `/home/marius/projects/billable/` as projects move to `/home/deploy/`

### Part 3: Org Infrastructure Setup

These must be in place before repo transfers begin.

#### GitHub App

No GitHub App is currently installed on WILSCH-AI-SERVICES. The deployment convention requires an org-wide App for:
- Distinct automation identity (commits traceable as automation, not human)
- 1-hour auto-expiring tokens (no permanent credentials on server)
- Org-wide repo access (no per-repo deploy key setup)

**Action:** Create and install GitHub App on WILSCH-AI-SERVICES. Server-side `deploy` user uses App credentials via JWT token helper (as designed in CCI #646).

#### Branch Protection Rulesets

No org-level rulesets exist. The convention requires:
- Require PR before merge on `staging` and `main`
- Block force push
- Single ruleset covering all current + future repos

**Action:** Create org-level ruleset targeting default branch pattern. Covers all repos — including those transferred later.

#### Team Plan Seats

Currently 2 members. After transfers, DaveX2001 needs org membership to maintain admin access on transferred repos. d3xma may need access depending on role.

**Action:** Invite DaveX2001 to org. Evaluate d3xma access needs. ($4/user/month)

### Part 4: Migration Sequencing & Dependencies

#### Dependency Map

```
Phase 0: Org Infrastructure (no dependencies)
├── GitHub App install
├── Branch protection rulesets
└── DaveX2001 org invite

Phase 1: Low-risk client repos (MariusWilsch → org)
├── rohdex (transfer only, no convention)
├── ARCHIBUS__archibus-poc (transfer, then deprecate)
├── Call2Tanss (transfer + convention adopt)
└── uwi__invoice-agent (transfer + convention adopt, blocked by this issue)

Phase 2: Internal/tooling repos (MariusWilsch → org)
├── public-wilsch-ai-pages (canonical copy)
├── claude-code-conversation-store
└── 00_WILSCH-AI-INTERNAL__soloforce

Phase 3: High-risk repos (DaveX2001 → org) — LAST
├── claude-code-improvements
└── deliverable-tracking (1300+ issues, most cross-refs)

Phase 4: Server-side consolidation (parallel with Phases 1-3)
├── Fresh clone under /home/deploy/projects/ for each transferred repo
├── Update git remotes (even though GitHub redirects work)
└── Legacy cleanup (remove old checkouts, stop old containers)

Phase 5: Reference updates (after all transfers)
├── CLAUDE.md files (hardcoded DaveX2001/deliverable-tracking paths)
├── CI/CD workflows
└── SSH configs
```

#### Per-Transfer Procedure

For each repo transfer:

1. **Pre-transfer:** Verify no active PRs or running workflows
2. **Transfer:** Owner initiates via GitHub Settings → Transfer (MariusWilsch or DaveX2001 must do this — only repo owner can transfer)
3. **Post-transfer:** Verify redirect works (`curl -sI old-url | grep Location`)
4. **Server-side:** Clone fresh under `/home/deploy/projects/` as `deploy` user with `dev-team` group
5. **Remote update:** `git remote set-url origin` on all existing checkouts (don't rely on redirect)
6. **Verify:** Containers still running, deployments still working

#### GitHub Redirect Risk

Redirects are **indefinite** but **fragile**. They break permanently if anyone creates a new repo at the old path (e.g., `DaveX2001/deliverable-tracking`). Mitigation:
- Do NOT create repos at old paths after transfer
- Document this risk for all team members
- Update references proactively (Phase 5) rather than relying on redirects long-term
- GitHub Pages URLs do NOT redirect — update hippocampus publishing config if public-wilsch-ai-pages moves

#### Convention Adoption Per Project

For projects classified as "Transfer + adopt" (invoice-agent, call2tanss):
- Each gets its own sub-issue under the parent epic (#646)
- Follow the proven pattern: create `docker-compose.infra.yml` + `docker-compose.yml`
- Add `deploy.entry` labels, healthchecks, env-var ports (`:-0`)
- Wire `deploy-action` GHA workflow (2 variables: `domain-suffix` + `ssh-host`)
- Volume migration: Docker volumes are name-scoped — convention adoption creates new project-scoped volumes, clone from existing

**Infra/App classification per project:**

| Project | Needs infra compose? | App services |
|---------|---------------------|-------------|
| invoice-agent | No — standalone backend, no GPU/shared services | FastAPI backend |
| call2tanss | No — standalone telephony service | Flask/FastAPI backend |
| rohdex | No — standalone backend | Backend service |

### Part 5: Rollback Strategy

#### Per-Phase Rollback

| Phase | Rollback mechanism | Risk |
|-------|-------------------|------|
| **Phase 0** (Org infra) | Delete ruleset, uninstall App | Zero risk — additive only |
| **Phase 1-2** (Repo transfer) | Transfer repo back to original owner | Low — GitHub preserves everything. Redirect from new→old path works the same way. |
| **Phase 3** (DaveX2001 repos) | Transfer back to DaveX2001 | Medium — if new repo created at old path during transfer window, redirect breaks permanently |
| **Phase 4** (Server consolidation) | Old checkouts still exist until cleanup. Containers reference volumes by name, not path. | Low — no data loss unless cleanup already ran |
| **Phase 5** (Reference updates) | Git revert the CLAUDE.md / config changes | Low — all changes are in version control |

#### Critical Rule

**Never create a repo at an old path after transfer.** This is the single failure mode that makes rollback impossible. Document for all team members: after `DaveX2001/deliverable-tracking` transfers, nobody creates a new repo called `deliverable-tracking` under `DaveX2001`.

#### Downtime Expectations

- **Repo transfers:** Zero downtime. GitHub redirects are instant. Git operations continue working.
- **Server migration (transfer only):** Zero downtime. Fresh clone under `/home/deploy/`, update remote on old checkout. Containers keep running.
- **Convention adoption:** Managed downtime per project. New compose brings up convention-compliant containers, old containers stopped after verification. Each project's adoption is its own sub-issue with its own rollback plan.

---

## Source

- **Transcripts:**
  - [Grooming + Shutdown Ritual 2026-03-29](https://app.fireflies.ai/view/01KMWSYYCKJFM54FFQC8MKBPAX) — migration scoping, project prioritization
  - [Main Session 2026-03-29](https://app.fireflies.ai/view/01KMWVD13DSDJWMFQRZ5MADJ32) — deploy-action walkthrough, infra/app convention, per-project classification
- **Artifacts:**
  - [CCI #646 Design Doc — Deployment & Runtime State](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-runtime-state-design) — parent convention this migration builds on
  - [WILSCH-AI-SERVICES/iitr-platform](https://github.com/WILSCH-AI-SERVICES/iitr-platform) — reference implementation (deploy.yml, docker-compose.yml, docker-compose.infra.yml)
  - [WILSCH-AI-SERVICES/deploy-action](https://github.com/WILSCH-AI-SERVICES/deploy-action) — reusable GHA workflow
  - [Issue #1318](https://github.com/DaveX2001/deliverable-tracking/issues/1318) — tracking issue
  - [Epic #646](https://github.com/DaveX2001/claude-code-improvements/issues/646) — parent epic
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/7e2cfb0d-ae02-4163-83b2-0c3e8975382d.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

