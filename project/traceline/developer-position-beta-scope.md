---
publish: true
---

# Developer Position Beta — Plugin Scope & Delivery
[[traceline]]

Design decisions for shipping the Developer Position as a beta product. Scope: what goes in the plugin, how users interact, and architectural choices.

---

## Problem Statement

The Developer Operations Manual defines a complete workflow (Path A: Spec-Design, Path B: Spec-Implement with Implementation + Verification sessions). This workflow is implemented as Claude Code skills and commands built over months of internal use. Shipping it to 10 beta users requires determining which components are load-bearing for the experience versus personal customization, and how to package them without exposing internal infrastructure (GitHub as backend, tracking repos, context trickle automation).

**Preconditions:**
- Developer Operations Manual v3 exists and defines the target experience
- Plugin delivery is the only viable distribution method (Claude Code plugin system)
- GitHub Issues is the persistence layer (beta decision, not permanent)
- Beta audience is ~10 developers, not general public
- Claude Code's native plan mode conflicts with the system's own planning workflow

---

## Success Definition

- **Goal:** A beta user installs the plugin and completes Path A (spec-design) + Path B (spec-implement + verify) on their own project without Marius explaining infrastructure
- **Success:** Beta users run the workflow independently; friction points surface through flag-for-improvement, not through confusion
- **Done test:** Christoph builds and completes the tutorial without asking Marius for clarification → plugin is ready for first 1-2 users
- **Undefined: Rollout cadence** — staged rollout: 1-2 users first, trickle to 10 over ~2 weeks. Criteria for expanding from 2 → 10 undefined. → See [Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/meeting-agenda-beta-launch-scope-2026-02-16)

---

## Approach

### Part 1: Plugin File Inventory

The Developer Operations Manual is the inclusion filter. Every file that maps to a manual step is load-bearing; everything else is cut.

**Shared (all sessions):**

| Component | Type | Beta Status |
|-----------|------|-------------|
| `/onboarding` | Command | Ship (simplified) |
| `/issue-comment` | Command | Ship as-is |
| `/flag-for-improvement` | Command | Ship (modified routing) |
| `deliverable-tracking` | Skill | Ship as-is |

**Path A: Spec-Design**

| Component | Type | Required? | Beta Status |
|-----------|------|-----------|-------------|
| `/rubber-duck` | Command | Required | Ship as-is |
| `ac-create` | Skill | Required | Ship (both Scout phases, Skip option) |

**Path B — Implementation Session:**

| Component | Type | Required? | Beta Status |
|-----------|------|-----------|-------------|
| `/rubber-duck` | Command | Optional | Ship as-is |
| `/requirements-clarity` | Command | Required | Ship (remove discovery script) |
| `/implementation-clarity` | Command | Required | Ship (keep discovery script — worktree) |
| `/evaluation-clarity` | Command | Required | Ship (remove discovery script) |
| `worktree` | Skill | Optional | Ship (ask in onboarding) |

**Path B — Verification Session:**

| Component | Type | Required? | Beta Status |
|-----------|------|-----------|-------------|
| `/rubber-duck` | Command | Optional | Ship as-is |
| `/ac-verify` | Command | Required | Ship as-is |
| `/merge` | Command | Required | Ship as-is |

**Cut for beta:**
- Document templates (backend/frontend README fetch)
- Quick investigation mode in onboarding (keep Full + Skip only)
- Auto-forward between clarity stages
- Discovery script in requirements-clarity and evaluation-clarity
- Transcript agent, email agent, design doc link agent (context trickle)
- Hippocampus knowledge management
- Shutdown ritual, create-contract, emyth, create-pflichtenheft

### Part 2: Onboarding Simplification

Beta onboarding keeps: session context bootstrap (repo + project only), protocol understanding (sequential thinking), brief introduction, issue selection from GitHub, investigation agent.

**Investigation:** Ask with time estimate (~60s). Options: Full / Skip. Quick mode removed.

**Cut:** detected label validation, SSH hosts, GH extensions, CLIs, document template selection, collaborator display. Bootstrap reduced to: which repo, which project, which issues exist.

### Part 3: Confidence & Handoff Loop

**Rubber-duck confidence signal** lives in the skill itself (line 61, lines 67-71). Four exit options: Quick fix, Let's plan, Define ACs, Keep thinking. Ships as-is.

**AC Create Scout:** Both phases (Scout Agent + Investigator Agent) ship with existing Skip option. Beta feedback determines whether to cut later.

**AC Create Refinement Gate:** All 4 checks stay (Observable Output, Test Data, Design Questions, Blocking Dependencies).

### Part 4: Clarity Workflow

Three phases ship. No auto-forward — user drives timing.

Discovery script kept in `/implementation-clarity` only (worktree). Removed from `/requirements-clarity` (hook enforces deliverable-tracking) and `/evaluation-clarity` (discovers nothing).

### Part 5: Execute & Task Complete

**Worktree:** Optional. Asked in onboarding. No worktree = no branch enforcement, no draft PR. User's responsibility.

**Task Complete protocol** extended:
1. Push to remote (evidence checkpoint)
2. Show commit hash link + issue link
3. AI proposes next step based on session work
4. AskUserQuestion — "Post as issue comment? Yes / No"
5. If yes → `/issue-comment` with proposed next step

**Undefined: DoD update in tracking.md** — untested. Marius verifying this week. → See [Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/meeting-agenda-beta-launch-scope-2026-02-16)

### Part 6: GitHub Integration & Repo Architecture

**Principle:** GitHub is backend-only long-term. For beta, users WILL interact with GitHub Issues directly (reading issue body, tracking.md, comments). Long-term vision: own UI replaces GitHub entirely.

**Issue-commit-linker:** Ships for beta. Same-repo = GITHUB_TOKEN sufficient.

**Spec reading:** Local markdown files (tracking.md). GitHub Pages deferred.

**Undefined: Repo model** — shared plugin repo vs per-user forks. CLI abstracts storage regardless. → See [Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/meeting-agenda-beta-launch-scope-2026-02-16)

### Part 7: Flag-for-Improvement (Beta Feedback)

Routes to CCI via GitHub Action workflow dispatch in plugin repo. PAT stored as repo secret. `github.actor` captures username. GitHub App (Traceline Bot) deferred until user-facing visibility exists.

**Undefined: Voice note feedback mechanism** → See [Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/meeting-agenda-beta-launch-scope-2026-02-16)

---

## Source

- **Transcript:** [Developer Position Launch Deep Dive (2026-02-15)](https://app.fireflies.ai/view/01KHG1Y7QQD62BVT24755BR552)
- **Operations Manual:** [Developer Operations Manual v3](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services)
- **Meeting Agendas:**
  - [Product Vision & Launch (2026-02-14)](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/meeting-agenda-product-vision-launch-2026-02-14)
  - [Developer Launch Deep Dive (2026-02-15)](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/meeting-agenda-developer-launch-deep-dive-2026-02-15)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/3cc5c4cd-ca43-45fc-962d-80081a8dd692.jsonl`
