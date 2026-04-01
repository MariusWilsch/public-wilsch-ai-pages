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

**WILSCH-AI-SERVICES** (9 repos, already in org):
- `03_IITR__iitr-platform` — IITR chat + RAG (convention-compliant)
- `01_ARCHIBUS__archibus-bulk-import` — Archibus bulk data entry (convention-compliant, proven)
- `deploy-action` — Reusable GHA deployment workflow (infra, public)
- `issue-commit-linker` — Commit-to-issue linking (infra)
- `wilsch-ai-team-plugin` — Claude Code team plugin (infra)
- `IITR__IITR-RAG-Navigation`, `IITR__IITR-RAG-Court-Judgments`, `IITR__IITR-RAG-Masterfragen`, `IITR__iitr-infrastructure` — IITR RAG subprojects (**archived**, old naming convention)

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
- `claude-code-improvements` — Stays on DaveX2001. Will be retired when CCI-600 completes.
- `public-wilsch-ai-pages` — Hippocampus mirror (non-canonical)

**Out of scope:** vibe-marketing, eberle-plugins, moltbot — personal/non-client repos stay in personal accounts.

#### Repository Naming Convention

Standardized pattern: `{NN}_{CLIENT}__{project}` — client repos use the numbered prefix matching the deliverable-tracking label scheme. Infra repos (no client owner) have no prefix.

| Type | Pattern | Example |
|------|---------|---------|
| **Client repo** | `{NN}_{CLIENT}__{project}` | `03_IITR__iitr-platform` |
| **Infra repo** | `{project}` (no prefix) | `deploy-action`, `issue-commit-linker` |

Active org repos already follow this convention. Transfers adopt it on arrival.

| Current Name | New Name (in org) | Rationale |
|-------------|-------------------|-----------|
| `uwi__invoice-agent` | `02_UWI__invoice-agent` | UWI client + project |
| `Call2Tanss` | `02_UWI__call2tanss` | UWI telephony integration |
| `ARCHIBUS__archibus-poc` | `01_ARCHIBUS__archibus-fm-assistant` | Not a PoC — it's the FM Assistant product |
| `rohdex` | `04_ROHDEX__rohdex` | Rohdex client |
| `klimafolgenschutz-website` | ✅ Already renamed: `06_ABTMAYR-REICHERT__klimafolgenschutz-website` | Done via #1338 |
| `wilsch-ai-site` | ✅ Already renamed: `00_WILSCH-AI-INTERNAL__wilsch-ai-site` | Done via #1338 |

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

#### Server — Running Containers

Port assignments and container counts are JIT-discoverable via `docker ps` — not maintained here. Grouped by convention status:

| Group | Projects | Status |
|-------|----------|--------|
| **Convention-compliant** | archibus-bulk-import | ✅ Auto-ports, healthchecks |
| **Needs convention adopt** | FM Assistant, invoice-agent, call2tanss, rohdex, klimafolgenschutz-cms | ❌ Hardcoded ports |
| **Out of scope** | moltbot, docker-socket-proxy, buildkit | Personal / infrastructure |

### Part 2: Per-Project Migration Classification

Each project gets one of three migration paths:

| Path | What it means | Example |
|------|--------------|---------|
| **Transfer only** | Move repo to org + checkout to `/home/deploy`. No compose changes. | wilsch-ai-site |
| **Transfer + adopt** | Move repo + restructure to convention (compose split where needed) + wire deploy-action. | invoice-agent, call2tanss, rohdex |
| **Already done** | Already in org and convention-compliant. | archibus-bulk-import, iitr-platform |

#### Classification Table

| Project | New Org Name | GitHub Transfer | Server Migration | Convention Adopt | Infra Compose? | Priority | Notes |
|---------|-------------|----------------|-----------------|-----------------|----------------|----------|-------|
| **archibus-bulk-import** | `01_ARCHIBUS__archibus-bulk-import` | ✅ Done | ✅ Done | ✅ Done | No — self-contained stack | — | First adopter, proven |
| **iitr-platform** | `03_IITR__iitr-platform` | ✅ Done | On IITR-STAGING server | ✅ Done | Yes (GPU services + Langfuse) | — | Separate server |
| **invoice-agent** | `02_UWI__invoice-agent` | MariusWilsch → org | `/home/marius/...` → `/home/deploy/` | Adopt | No — standalone FastAPI backend | High | Marius: "block invoice-agent davon" |
| **call2tanss** | `02_UWI__call2tanss` | MariusWilsch → org | `/home/marius/...` → `/home/deploy/` | Adopt | No — standalone Flask/FastAPI | Medium | UWI telephony integration |
| **archibus-fm-assistant** | `01_ARCHIBUS__archibus-fm-assistant` | ✅ Done | `/home/shared/` → `/home/deploy/` | Adopt | Recommended: Chromote + MongoDB = infra, LibreChat + FastMCP = app | **First candidate** | First migration to validate approach. Absorbs #1061 (documentation deliverable for client ops still outstanding) |
| **rohdex** | `04_ROHDEX__rohdex` | MariusWilsch → org | `/home/shared/` → `/home/deploy/` | Adopt | No — standalone backend | Low | Benefits from convention (auto-ports, healthchecks) |
| **klimafolgenschutz** | `06_ABTMAYR-REICHERT__klimafolgenschutz-website` | ✅ Done (#1338) | `/home/david/...` → `/home/deploy/` | Adopt | No — Payload CMS + dedicated Postgres | Low | Benefits from convention (auto-ports, healthchecks, push=preview) |
| **wilsch-ai-site** | `00_WILSCH-AI-INTERNAL__wilsch-ai-site` | ✅ Done (#1338) | No server component | N/A | N/A | Low | Company website |

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
├── ARCHIBUS__archibus-poc → 01_ARCHIBUS__archibus-fm-assistant — ✅ Transferred (adopt pending) — FIRST CANDIDATE
├── uwi__invoice-agent → 02_UWI__invoice-agent (transfer + adopt) — HIGH
├── Call2Tanss → 02_UWI__call2tanss (transfer + adopt) — MEDIUM
├── rohdex → 04_ROHDEX__rohdex (transfer + adopt) — LOW
├── klimafolgenschutz-website → 06_ABTMAYR-REICHERT__klimafolgenschutz-website (transfer + adopt) — LOW
└── wilsch-ai-site → ✅ Done (#1338)

Phase 2: Server-side consolidation (parallel with Phase 1)
├── Pre-transfer: pin COMPOSE_PROJECT_NAME in staging .env (prevents volume orphaning)
├── Self-healing preflight auto-clones on first GHA deploy (no manual clone needed)
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

1. **Pre-transfer:** Pin `COMPOSE_PROJECT_NAME` in staging `.env` — directory rename changes the default project name, orphaning existing volumes. Previews already get explicit names from deploy script; only staging needs pinning. (Lesson from CCI #646 — [comment](https://github.com/DaveX2001/claude-code-improvements/issues/646#issuecomment-4160133314).)
2. **Pre-transfer:** Verify no active PRs or running workflows
3. **Transfer:** Owner initiates via GitHub Settings → Transfer. For personal repos (`MariusWilsch/`): only the account owner can transfer. ([GitHub docs](https://docs.github.com/en/repositories/creating-and-managing-repositories/transferring-a-repository))
4. **Rename:** After transfer, rename to convention name (e.g., `ARCHIBUS__archibus-poc` → `01_ARCHIBUS__archibus-fm-assistant`)
5. **Post-transfer:** Verify redirect works (`curl -sI old-url | grep Location`)
6. **First push:** Self-healing preflight (CCI #646 Level 0s) auto-creates `/home/deploy/projects/{new-name}`, clones repo, sets permissions — no manual clone needed
7. **Verify:** Containers running, healthchecks passing, App + ruleset applied

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

Two-layer delivery model, matching how conventions already propagate in the team:

**Layer 1: Plugin Belief Block (universal rules)**

Lives in `wilsch-ai-team-plugin` CLAUDE.md as a `<docker_compose_convention>` block — same pattern as the existing `<makefile_check>`. Activates via WHEN/WHY/Behavior conditioning: the AI reads the rule and applies it when compose file context is detected. No path-spec matching needed.

Contains:
- Port convention: `${PORT:-0}` (auto-assigned, no hardcoded ports)
- No `container_name:` in app compose (prevents preview collisions)
- Healthcheck required on every service (default: [microcheck](https://github.com/tarampampam/microcheck))
- `deploy.entry` label on entry-point service
- Volume naming: Docker Compose project convention (no explicit `name:`)
- Compose split: `docker-compose.yml` (app) + `docker-compose.infra.yml` (infra, if needed)

**Layer 2: Template Repo (project-specific skeleton)**

WILSCH-AI-SERVICES template repo seeded with:
- 12-line `deploy.yml` GHA workflow (see CCI #646 Part 7)
- Convention-compliant compose skeleton
- `.env.example` with standard variables
- CLAUDE.md "Compose Architecture" section template (as seen in IITR and Archibus projects)

**Precedent:** Both layers already exist in practice. IITR and Archibus project CLAUDE.md files contain project-specific compose architecture sections. The team plugin already carries cross-project belief blocks (`<makefile_check>`). This part formalizes the pattern.

**Deliverable:** Plugin belief block created alongside FM Assistant migration (first candidate). Template repo created as CCI #646 Undefined Item #5.

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
  - [SA review pass (2026-04-01)](https://github.com/DaveX2001/deliverable-tracking/issues/1318#issuecomment-4166883055) — 14 comments: naming convention, scope guard, convention artifact, volume pinning, FM Assistant as first candidate, template repo
- **Artifacts:**
  - [CCI #646 Design Doc — Deployment & Runtime State](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-runtime-state-design) — parent convention this migration builds on
  - [WILSCH-AI-SERVICES/iitr-platform](https://github.com/WILSCH-AI-SERVICES/iitr-platform) — reference implementation (deploy.yml, docker-compose.yml, docker-compose.infra.yml)
  - [WILSCH-AI-SERVICES/deploy-action](https://github.com/WILSCH-AI-SERVICES/deploy-action) — reusable GHA workflow
  - [microcheck](https://github.com/tarampampam/microcheck) — lightweight healthcheck binary for curl-less containers
  - [Issue #1318](https://github.com/DaveX2001/deliverable-tracking/issues/1318) — tracking issue
  - [Epic #646](https://github.com/DaveX2001/claude-code-improvements/issues/646) — parent epic
  - [Issue #1061](https://github.com/DaveX2001/deliverable-tracking/issues/1061) — Monitoring & Debug Documentation Revision (closed, merged into #1318). FM Assistant documentation deliverable still outstanding.
  - [Issue #1338](https://github.com/DaveX2001/deliverable-tracking/issues/1338) — Rename repos + grant admin access (completed)
  - [docs/infrastructure.md](https://github.com/DaveX2001/deliverable-tracking/blob/main/docs/infrastructure.md) — Archibus infrastructure reference (historic context, stale)
- **Sessions:**
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/7e2cfb0d-ae02-4163-83b2-0c3e8975382d.jsonl (original design)
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/0a41eb45-8096-4c51-a4a6-641a48cf808b.jsonl (review feedback incorporation)
  - [Session 7e9dc88b (MariusWilsch)](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/7e9dc88b-4d23-45ba-a216-a90a2ef4d407.jsonl) — SA review session (2026-04-01)
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/cbd361ea-cc30-4b62-9369-601a70312b47.jsonl (v3 extraction pass)
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/8bef9005-45f4-4e1d-b958-78bba8264eb9.jsonl (v4 corrections — klimafolgenschutz reclassification, CCI retirement, FM Assistant transfer status)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

