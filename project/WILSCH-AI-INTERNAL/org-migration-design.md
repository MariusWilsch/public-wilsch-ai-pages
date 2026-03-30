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
- **Containers:** 29 running containers, most with hardcoded ports and manual deployment. Only `archibus-bulk-import` uses the convention's auto-assigned ports and health checks. (`issue-1190-*` preview containers are being merged and will be removed.)

**What this IS:** A consolidation plan — moving repos to one org, checkouts to one path, and classifying each project's convention adoption path.

**What this ISN'T:** A redesign of the deployment convention itself (that's CCI #646). This doc assumes the convention is proven and focuses on migration.

**Preconditions:**
- The deployment convention (infra/app split, deploy-action, push=preview) is validated — archibus-bulk-import passed 7/7 ACs (#1251), iitr-platform adopted the compose split (#1281)
- `deploy` user exists on server (uid=1005, docker + dev-team groups)
- WILSCH-AI-SERVICES org is on Team plan (2 members, org-level rulesets available)
- `deploy-action` reusable workflow exists and is tested
- Two GitHub Apps already installed org-wide: `issue-commit-linker` (App ID 2395636) and `wilsch-ai-deploy` (App ID 3206533)
- One org-level ruleset active: ID 14416635, "Protected branches — require PR, block force push" (created 2026-03-27)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | All active client repos live under WILSCH-AI-SERVICES with standardized naming. All server-side project checkouts live under `/home/deploy/projects/` with `deploy:dev-team` ownership. Each project classified for convention adoption. |
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

**MariusWilsch** (client repos to transfer):
- `uwi__invoice-agent` — UWI invoice processing → rename to `uwi-invoice-agent`
- `Call2Tanss` — Call2Tanss telephony integration → rename to `call2tanss`
- `ARCHIBUS__archibus-poc` — Archibus FM Assistant (separate project from archibus-bulk-import) → rename to `archibus-fm-assistant`
- `rohdex` — Rohdex backend → stays `rohdex`
- `klimafolgenschutz-website` — Klimafolgenschutz CMS → transfer to org
- `wilsch-ai-site` — Company website → transfer to org

**MariusWilsch** (stay on personal account):
- `00_WILSCH-AI-INTERNAL__soloforce` — Marius's working directory
- `public-wilsch-ai-pages` — Hippocampus publishing (will be replaced by new publishing workflow)
- `claude-code-conversation-store` — Session audit trail

**DaveX2001** (stay on personal account):
- `deliverable-tracking` — **Deferred indefinitely.** 1300+ issues, all agents reference it, highest redirect risk, low ROI now.
- `claude-code-improvements` — Stays. Will be retired when CCI-600 completes.
- `public-wilsch-ai-pages` — Hippocampus mirror (non-canonical)

**Out of scope:** vibe-marketing, eberle-plugins, moltbot — personal/non-client repos stay in personal accounts.

#### Repository Naming Convention

Standardized pattern: `{client}-{project}` (lowercase, hyphen-separated). Reference: [WILSCH-AI-SERVICES/iitr-platform](https://github.com/WILSCH-AI-SERVICES/iitr-platform).

| Current Name | New Name (in org) | Rationale |
|-------------|-------------------|-----------|
| `uwi__invoice-agent` | `uwi-invoice-agent` | Client + project |
| `Call2Tanss` | `call2tanss` | Lowercase standardization |
| `ARCHIBUS__archibus-poc` | `archibus-fm-assistant` | Not a PoC — it's the FM Assistant product |
| `rohdex` | `rohdex` | Already follows pattern |
| `klimafolgenschutz-website` | `klimafolgenschutz-website` | Already follows pattern |
| `wilsch-ai-site` | `wilsch-ai-site` | Already follows pattern |

#### Server — Project Checkouts (as of 2026-03-29)

| Project | Path | Owner | Remote | Convention? |
|---------|------|-------|--------|-------------|
| archibus-bulk-import | `/home/deploy/projects/` | deploy:dev-team | WILSCH-AI-SERVICES ✅ | ✅ Yes |
| archibus-bulk-import (dup) | `/home/shared/projects/` | marius:dev-team | WILSCH-AI-SERVICES | Legacy copy |
| archibus_full-stack (FM Assistant) | `/home/shared/projects/` | marius:dev-team | MariusWilsch/ARCHIBUS__archibus-poc | ❌ Active, needs convention adopt |
| archibus-deploy | `/home/marius/projects/billable/` | marius | No remote (LibreChat upstream clone) | ❌ Legacy |
| call2tanss_full-stack | `/home/marius/projects/billable/` | marius | MariusWilsch/Call2Tanss | ❌ No |
| invoice-agent_backend | `/home/marius/projects/billable/` | marius | MariusWilsch/uwi__invoice-agent | ❌ No |
| rohdex | `/home/shared/` | ? | MariusWilsch/rohdex | ❌ No |

#### Server — Running Containers (grouped)

**Convention-compliant (auto-ports, healthchecks):**
- `archibus-bulk-import-*` (3 containers) — staging

**FM Assistant / Archibus (hardcoded ports):**
- `LibreChat-staging` (:3081), `LibreChat-dev` (:3080) — Archibus FM Assistant chat UI
- `archibus_fastmcp_staging` (:8001) — Archibus FM Assistant MCP
- `chat-mongodb-*`, `rag_api-*`, `vectordb-*`, `chat-meilisearch-*` — supporting services

**Client projects (hardcoded ports):**
- `invoice-production-backend` (:8081) — UWI production
- `rohdex-staging` (:9000)
- `call2tanss` (:5000), `call2tanss-dev` (:5001), `call2tanss-staging` (:5002)

**Client projects (hardcoded ports, continued):**
- `klimafolgenschutz-cms` (:3100) — Klimafolgenschutz CMS

**Out of scope (stay as-is):**
- `moltbot-*` (:18789-90) — personal project
- `docker-socket-proxy`, `buildkit`, `buildx_buildkit_*`, `metamcp-pg` — infrastructure

### Part 2: Per-Project Migration Classification

Each project gets one of three migration paths:

| Path | What it means | Example |
|------|--------------|---------|
| **Transfer only** | Move repo to org + checkout to `/home/deploy`. No compose changes. | klimafolgenschutz-website |
| **Transfer + adopt** | Move repo + restructure to convention (compose split where needed) + wire deploy-action. | invoice-agent, call2tanss, rohdex |
| **Already done** | Already in org and convention-compliant. | archibus-bulk-import, iitr-platform |

#### Classification Table

| Project | New Org Name | GitHub Transfer | Server Migration | Convention Adopt | Infra Compose? | Priority | Notes |
|---------|-------------|----------------|-----------------|-----------------|----------------|----------|-------|
| **archibus-bulk-import** | (unchanged) | ✅ Done | ✅ Done | ✅ Done | Yes (shared DB) | — | First adopter, proven |
| **iitr-platform** | (unchanged) | ✅ Done | On IITR-STAGING server | ✅ Done | Yes (Postgres + Redis) | — | Separate server (136.243.71.58) |
| **invoice-agent** | `uwi-invoice-agent` | MariusWilsch → org | `/home/marius/...` → `/home/deploy/` | Adopt | No — standalone FastAPI backend | High | Marius: "block invoice-agent davon" |
| **call2tanss** | `call2tanss` | MariusWilsch → org | `/home/marius/...` → `/home/deploy/` | Adopt | No — standalone Flask/FastAPI | Medium | 3 running instances with hardcoded ports |
| **archibus-fm-assistant** | `archibus-fm-assistant` | MariusWilsch → org | `/home/shared/` → `/home/deploy/` | Adopt | Evaluate (LibreChat + MongoDB + supporting services) | Medium | NOT legacy — separate product from archibus-bulk-import |
| **rohdex** | `rohdex` | MariusWilsch → org | `/home/shared/` → `/home/deploy/` | Adopt | No — standalone backend | Low | Benefits from convention (auto-ports, healthchecks) |
| **klimafolgenschutz** | `klimafolgenschutz-website` | MariusWilsch → org | Has CMS container | Transfer only | No | Low | CMS container stays as-is |
| **wilsch-ai-site** | `wilsch-ai-site` | MariusWilsch → org | No server component | N/A | N/A | Low | Company website |

**Not transferring (deferred or staying):**

| Project | Decision | Rationale |
|---------|----------|-----------|
| `deliverable-tracking` | **Deferred indefinitely** | 1300+ issues, all agents reference it, highest redirect risk, low ROI now |
| `claude-code-improvements` | **Stays on DaveX2001** | Will be retired when CCI-600 completes |
| `public-wilsch-ai-pages` | **Stays on MariusWilsch** | Will be replaced by new publishing workflow |
| `claude-code-conversation-store` | **Stays on MariusWilsch** | Audit trail, no transfer benefit |
| `00_WILSCH-AI-INTERNAL__soloforce` | **Stays on MariusWilsch** | Marius's working directory |

#### Legacy Cleanup (post-migration)

After convention-compliant replacements are running:
- **Remove** `/home/shared/projects/archibus-bulk-import` (duplicate of `/home/deploy/`)
- **Remove** `/home/marius/projects/billable/archibus-deploy` (dead LibreChat clone, no remote)
- **Remove** old checkouts from `/home/marius/projects/billable/` as projects move to `/home/deploy/`

**Final cleanup — `docker system prune`:** Run only AFTER all migrations complete and all convention-compliant containers are verified running. Do NOT prune during migration to avoid deleting resources needed by containers being migrated (lesson from IITR migration).

### Part 3: Org Infrastructure Setup

All org-level infrastructure is already in place. No setup actions required before repo transfers begin.

#### GitHub Apps — ✅ Already Installed

Two GitHub Apps are already installed org-wide on WILSCH-AI-SERVICES (both `repository_selection: all`):

| App | App ID | Permissions | Purpose |
|-----|--------|------------|---------|
| `issue-commit-linker` | 2395636 | contents:read, issues:write | Links commits to issues |
| `wilsch-ai-deploy` | 3206533 | actions:write, contents:write, issues:write | Deployment automation |

Queryable via `gh api /orgs/WILSCH-AI-SERVICES/installations`.

The `wilsch-ai-deploy` App is used via [`actions/create-github-app-token@v1`](https://github.com/actions/create-github-app-token) in GitHub Actions — no custom JWT helper. Token flow: org secrets (`APP_ID`, `APP_PRIVATE_KEY`) → `create-github-app-token` → ephemeral 1hr token → injected into SSH commands → deploy scripts use HTTPS + token for git pull. No permanent git credentials on any server.

**Action:** No new App creation needed. Transferred repos automatically inherit org-wide App access — no per-repo installation required.

#### Branch Protection Rulesets — ✅ Already Active

One org-level ruleset exists (queryable via `gh api /orgs/WILSCH-AI-SERVICES/rulesets` — requires `admin:org` scope):

| ID | Name | Enforcement | Created |
|----|------|-------------|---------|
| 14416635 | Protected branches — require PR, block force push | Active | 2026-03-27 |

**Action:** No new ruleset creation needed. Transferred repos automatically inherit the org-level ruleset. Verify after each transfer that the ruleset applies to the new repo's default branch.

#### Team Plan & Access

Currently 2 seats, both filled (Team plan).

| Person | Action | Cost |
|--------|--------|------|
| DaveX2001 | Invite to org (admin access on transferred repos) | $4/user/month |
| d3xma | **No access needed** | — |

**Note on collaborator costs:** Free GitHub users CAN be outside collaborators on public repos at no cost. Private repos require a paid seat per collaborator ($4/user/month). All client repos are private → any additional collaborators require a seat.

### Part 4: Migration Sequencing & Dependencies

#### Dependency Map

```
Phase 0: Org Infrastructure
├── ✅ GitHub Apps — already installed
├── ✅ Branch protection ruleset — already active
└── DaveX2001 org invite

Phase 1: Client repos (MariusWilsch → org)
├── uwi__invoice-agent → uwi-invoice-agent (transfer + adopt) — HIGH
├── Call2Tanss → call2tanss (transfer + adopt) — MEDIUM
├── ARCHIBUS__archibus-poc → archibus-fm-assistant (transfer + adopt) — MEDIUM
├── rohdex → rohdex (transfer + adopt) — LOW
├── klimafolgenschutz-website (transfer only) — LOW
└── wilsch-ai-site (transfer only) — LOW

Phase 2: Server-side consolidation (parallel with Phase 1)
├── Fresh clone under /home/deploy/projects/ for each transferred repo
├── Volume migration: copy named volumes to convention-scoped names
└── Wire deploy-action per project (for "transfer + adopt" projects)

Phase 3: Legacy cleanup (AFTER all Phases 1+2 verified)
├── Remove old checkouts from /home/marius/ and /home/shared/
├── Stop replaced containers
└── docker system prune (final, only after everything verified)

Phase 4: Reference updates (after all transfers)
├── CLAUDE.md files (hardcoded repo paths)
├── CI/CD workflows
└── SSH configs
```

#### Per-Transfer Procedure

For each repo transfer:

1. **Pre-transfer:** Verify no active PRs or running workflows
2. **Transfer:** Owner initiates via GitHub Settings → Transfer. For personal repos (`MariusWilsch/`): only the account owner can transfer. For org repos: org owners OR repo admins can transfer. ([GitHub docs](https://docs.github.com/en/repositories/creating-and-managing-repositories/transferring-a-repository))
3. **Rename (if needed):** After transfer, rename repo to match naming convention (e.g., `ARCHIBUS__archibus-poc` → `archibus-fm-assistant`)
4. **Post-transfer:** Verify redirect works (`curl -sI old-url | grep Location`)
5. **Server-side:** Clone fresh under `/home/deploy/projects/{new-name}` as `deploy` user with `dev-team` group
6. **Volume migration:** If project has Docker volumes, copy data from old project-scoped volumes to new convention-scoped names. Use Docker Compose project naming convention — no explicit `name:` in compose files. (Same approach as IITR migration.)
7. **Verify:** Containers running, deployments working, App + ruleset applied

#### GitHub Redirect Risk

Redirects are **indefinite** but **fragile**. They break permanently if anyone creates a new repo at the old path (e.g., `MariusWilsch/uwi__invoice-agent`). Mitigation:
- Do NOT create repos at old paths after transfer
- Document this risk for all team members
- Update references proactively (Phase 4) rather than relying on redirects long-term

#### Convention Adoption Per Project

For projects classified as "Transfer + adopt":
- Each gets its own sub-issue under the parent epic (#646)
- Not every project needs an infra/app split — see Infra Compose column in Part 2 classification table
- App-only projects: single `docker-compose.yml` with `deploy.entry` labels, healthchecks, env-var ports (`:-0`)
- Infra+app projects: `docker-compose.infra.yml` + `docker-compose.yml`
- Wire `deploy-action` GHA workflow (2 variables: `domain-suffix` + `ssh-host`)
- Volume migration: use Docker Compose project naming convention (no explicit `name:` in compose files). Copy existing volume data once during migration — same approach as IITR migration.

#### Healthcheck Strategy

Many containers lack `curl` or HTTP utilities, making traditional healthchecks difficult. Recommended approaches (in order of preference):

| Approach | How | Size | Trade-off |
|----------|-----|------|-----------|
| **microcheck** | Static binary, multi-stage copy into image | ~75KB | One consistent pattern for all projects regardless of language |
| **Runtime-native** | Use app's own runtime (Node `http.get`, Python `urllib`) | 0 | Per-language divergence, couples healthcheck to runtime |
| **netcat** | `nc -z localhost PORT` (busybox/Alpine images) | 0 | TCP-only — verifies port open, not app health |

**Standard:** Use [microcheck](https://github.com/tarampampam/microcheck) as the default healthcheck binary. Copy via multi-stage build into every image. Works in scratch, distroless, Alpine, Debian slim — any base image.

```dockerfile
COPY --from=tarampampam/microcheck:latest /usr/bin/httpcheck /usr/bin/httpcheck
HEALTHCHECK CMD ["/usr/bin/httpcheck", "http://localhost:8080/health"]
```

Each project's convention adoption sub-issue should specify which healthcheck approach to use based on its base image.

### Part 6: Convention Instruction Artifact

As part of this migration, create a minimalistic Docker Compose convention as an auto-inserted instruction artifact — a file that activates whenever the AI works with compose files, without bloating CLAUDE.md.

Reference: [Claude Code Rules — Stop Stuffing Everything into One CLAUDE.md](https://medium.com/@richardhightower/claude-code-rules-stop-stuffing-everything-into-one-claude-md-0b3732bca433)

**What the artifact defines:**
- Path spec: `/home/deploy/projects/{project}/`
- Compose convention: `docker-compose.yml` (app) + `docker-compose.infra.yml` (infra, if needed)
- Volume naming: Docker Compose project convention (no explicit `name:`)
- Port convention: env-var with `:-0` default (auto-assigned)
- Healthcheck requirement: every service must have a healthcheck (see Part 4 Healthcheck Strategy)
- Deploy labels: `deploy.entry` for routing

**Where:** Global `~/.claude/` instruction artifact or plugin-level. Auto-inserted when AI detects compose file context.

**Deliverable:** Created alongside the first "transfer + adopt" project to validate the pattern against real usage.

### Part 7: Rollback Strategy

#### Per-Phase Rollback

| Phase | Rollback mechanism | Risk |
|-------|-------------------|------|
| **Phase 0** (Org invite) | Remove member | Zero risk |
| **Phase 1** (Repo transfer) | Transfer repo back to MariusWilsch | Low — GitHub preserves everything. Redirect from new→old path works the same way. |
| **Phase 2** (Server consolidation) | Old checkouts still exist until cleanup. Containers reference volumes by name, not path. | Low — no data loss unless cleanup already ran |
| **Phase 3** (Legacy cleanup) | Cannot restore pruned images/volumes. Old checkouts gone. | **Medium — point of no return. Only execute after full verification.** |
| **Phase 4** (Reference updates) | Git revert the CLAUDE.md / config changes | Low — all changes are in version control |

#### Critical Rule

**Never create a repo at an old path after transfer.** This is the single failure mode that makes rollback impossible. Document for all team members.

#### Downtime Expectations

- **Repo transfers:** Zero downtime. GitHub redirects are instant. Git operations continue working.
- **Server migration (transfer only):** Zero downtime. Fresh clone under `/home/deploy/`, update remote on old checkout. Containers keep running.
- **Convention adoption:** Managed downtime per project. New compose brings up convention-compliant containers, old containers stopped after verification. Each project's adoption is its own sub-issue with its own rollback plan.

---

## Source

- **Transcripts:**
  - [Grooming + Shutdown Ritual 2026-03-29](https://app.fireflies.ai/view/01KMWSYYCKJFM54FFQC8MKBPAX) — migration scoping, project prioritization
  - [Main Session 2026-03-29](https://app.fireflies.ai/view/01KMWVD13DSDJWMFQRZ5MADJ32) — deploy-action walkthrough, infra/app convention, per-project classification
- **Review feedback:**
  - [Issue #1318 comments](https://github.com/DaveX2001/deliverable-tracking/issues/1318) — Marius's design doc walkthrough (2026-03-30), 6 comments covering per-project transfer decisions, classification, validated infra claims, convention artifact, server ops, access scope
- **Artifacts:**
  - [CCI #646 Design Doc — Deployment & Runtime State](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-runtime-state-design) — parent convention this migration builds on
  - [WILSCH-AI-SERVICES/iitr-platform](https://github.com/WILSCH-AI-SERVICES/iitr-platform) — reference implementation (deploy.yml, docker-compose.yml, docker-compose.infra.yml)
  - [WILSCH-AI-SERVICES/deploy-action](https://github.com/WILSCH-AI-SERVICES/deploy-action) — reusable GHA workflow
  - [microcheck](https://github.com/tarampampam/microcheck) — lightweight healthcheck binary for curl-less containers
  - [Issue #1318](https://github.com/DaveX2001/deliverable-tracking/issues/1318) — tracking issue
  - [Epic #646](https://github.com/DaveX2001/claude-code-improvements/issues/646) — parent epic
- **Sessions:**
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/7e2cfb0d-ae02-4163-83b2-0c3e8975382d.jsonl (original design)
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/0a41eb45-8096-4c51-a4a6-641a48cf808b.jsonl (review feedback incorporation)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

