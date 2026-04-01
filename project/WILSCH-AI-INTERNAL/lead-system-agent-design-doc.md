---
publish: true
---

# Lead System Agent — Design Doc
[[lead-system-agent]]

Design doc for a CLI-native, AI-accessible lead management system to replace the broken lead tracking in DaveX2001/deliverable-tracking. Covers tooling evaluation, lead lifecycle, deduplication, and boundary definition.

---

## Problem Statement

Lead tracking in DaveX2001/deliverable-tracking is structurally broken. Leads exist across multiple uncoordinated dimensions — the `lead` label (16 issues), the `[SALES-PIPELINE] Active Leads` milestone (16 issues), and ad-hoc epics — with zero overlap between them. A single lead can spawn 3+ artifacts (e.g., Stafford: #874 epic, #828 lead issue, #1078 design doc) with no canonical source of truth.

The system is purely reactive. Nobody drives lead lifecycle transitions — leads sit until an external trigger occurs or Marius manually engages. The pipeline view shows issues exist but doesn't drive action. The team doesn't look at lead issues regularly.

GitHub Issues is a proven tool for project/deliverable tracking but lacks CRM primitives: pipeline stages, lead lifecycle, relationship tracking between contacts and deals. The current setup forces CRM-shaped work into a project management tool.

**Preconditions:**
- Team is 2-3 people (Marius, David, + support)
- All project/deliverable work already lives in GitHub Issues and will stay there
- The AI agent (Claude Code) is the primary interface for all operations — CLI-native access is mandatory
- Lead management is the scope — not broader marketing ops (content, outreach, campaigns)
- No budget constraint identified, but self-hosting preferred over SaaS dependency

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A coherent lead management system where each lead has exactly one tracking artifact, Claude Code can manage leads via CLI, and the pipeline actively surfaces what needs attention — replacing the fragmented, reactive tracking in GitHub Issues. |
| **Success** | Marius can ask Claude "show me all leads in negotiation" and get a deterministic answer. Leads move through defined lifecycle stages. No duplicate artifacts per lead. The tool is as natural to operate from terminal as `gh issue list` is for project work. |
| **Done test** | Can Marius manage his entire lead pipeline without opening a browser? If YES for the common operations (create lead, move stage, query pipeline, log interaction) → done. If NO → identify which operations still require browser. |

---

## Approach

### Part 1: Tooling Fit — What the Agent Needs

Pass 1 evaluated CRM tools in isolation. The question has since shifted: not "which CRM?" but "what does a Lead Agent need underneath it — and does GitHub already provide enough?"

#### The agent is the product

The position epic is called Lead System **Agent**, not Lead System CRM. The problem statement identifies the system as "purely reactive — nobody drives lead lifecycle transitions." The success definition requires the pipeline to "actively surface what needs attention." Both point to the same fix: an agent that scans leads, tracks follow-up commitments, checks for evidence (emails, transcripts, issue comments), and pushes reminders when commitments go overdue. No tool switch fixes inaction — an agent does. The agent is present in every architecture below. The variable is what data layer it reads from.

#### GitHub Projects: what it already covers

GitHub Projects v2 can model a basic lead pipeline today:

| Capability | How GitHub does it |
|---|---|
| Pipeline stages | Single-select custom field (New → Qualified → Engaged → Converted → Dead), Kanban board columns |
| Lead metadata | Up to 50 custom fields: Company (text), Deal Value (number), Follow-up Date (date), Source (single-select) |
| Filtered views | Saved views with field filters — e.g., `follow-up:<=@today` shows overdue leads |
| CLI access | `gh project item-list`, `item-edit`, `item-create` — full CRUD, ID-driven |
| Automation | Built-in workflows (auto-set status on close) + GitHub Actions for custom logic |
| Capacity | 50,000 items per project — no scale concern |
| AI-readiness | `gh` CLI is 5+ years old, deeply embedded in pre-training data. Claude operates it natively without documentation lookup. |

#### The single gap: relational data

GitHub Projects is a flat item list with metadata fields — not a relational database. There is no concept of "Person belongs to Company."

**What this means concretely:** The FSO-Seminar (March 2026) produced 4 interested contacts — Pöckemöller, Hasloff, Arndt, and one unnamed. In GitHub, each becomes an issue. When Pöckemöller says "loop in my colleague at the same company," that colleague becomes issue #5 with no structural link to issue #1. The agent cannot query "show me all contacts at Pöckemöller's company." It also cannot cascade updates — changing a company name means editing every person issue individually.

At 15-20 leads with a team of 2-3, this gap is manageable with discipline. At 50+ leads, the flat model breaks down.

CRM tools solve this by design. Twenty CRM's very first release (v0.1.0, June 2023) shipped with Person and Company table views as foundational features — the relational data model is what a CRM IS.

#### What purpose-built CRM tools add

Beyond the relational model, CRM tools offer:

| Capability | GitHub | CRM (Twenty/Attio) | Agent can provide? |
|---|---|---|---|
| Person → Company → Deal model | No | Yes (foundational) | On lightweight index: yes |
| Typed interaction timeline | No (unstructured comments) | Yes (call, email, meeting types) | Partially — agent can tag comments |
| Pipeline Kanban board | Yes | Yes | N/A |
| CLI access | `gh` (5+ years, deep pre-training) | Community CLIs (newer, less pre-trained) | N/A |
| MCP integration | None (`gh` CLI is the integration) | Attio: official first-party MCP (20-21 tools). Twenty: 4 community MCPs (up to 29 tools) | N/A |
| Contact enrichment | No | Partial (Attio) | No |
| `/llms.txt` for AI consumption | No | Both Twenty and Attio provide it | N/A |
| Browser dashboard | GitHub Projects (basic) | Full CRM UI | Not needed — agent is the UI |

The capture pipeline (email-sync → GitHub issues, transcript-sync → GitHub issues) already flows interaction data into the system. The CRM doesn't add new data — it structures existing data relationally.

#### AI-readiness comparison

| Dimension | GitHub CLI | Twenty CRM | Attio CRM |
|---|---|---|---|
| **Pre-training depth** | 5+ years, every model knows `gh` | ~3 years, 43k stars, moderate | Newer, smaller community |
| **Documentation** | Extensive, stable | ~120 pages, `/llms.txt`, auto-generated API docs (4/5) | 85 endpoint pages, `/llms.txt`, OpenAPI 3.1.0 spec (4/5) |
| **CLI maturity** | Official, battle-tested | Community `twenty-cli` — `--help-json`, agent output format, pending official merge | Community `attio-cli` — 11 stars, functional |
| **MCP** | None | 4 community servers, no official | **Official first-party** MCP (rare) |
| **AI signal** | Pre-training dominance | `/llms.txt` + agent-native CLI | `/llms.txt` + OpenAPI + official MCP |

Marius's heuristic: "If there's no documentation, no easy way for AI to find this out — already a bad sign." Both Twenty and Attio pass this test with `/llms.txt` endpoints. Attio's official first-party MCP is the strongest AI integration signal in the CRM space. GitHub's pre-training advantage is unmatched but comes without CRM data primitives.

#### Three architectures

The agent is the constant. The variable is the data layer.

**Architecture A — Agent + GitHub (start here)**
Lead Agent operates on GitHub Issues in a dedicated Sales Pipeline project. Custom fields model pipeline stages, contact info, and follow-up dates. The agent scans for staleness, tracks commitments from transcripts and emails, pushes reminders. Zero new tools — existing `gh` commands, existing capture pipeline.
*Limitation:* No person-company relational queries. The agent works with flat issues.

**Architecture C — Agent + Lightweight Data Index**
Same agent, same GitHub Issues for tracking. Additionally, the agent maintains a small structured index (Supabase tables or YAML) mapping Person → Company → Issue. The relational layer exists for the agent to reason about ("all contacts at Maschinenfabrik"), but Marius doesn't learn a new tool — same `gh` commands, same workflow. The agent is the only consumer of the index.
*Limitation:* No human-facing dashboard for relational data. The agent IS the UI.

**Architecture B — Agent + CRM (graduation path)**
Lead data moves to Twenty or Attio. The agent reads from the CRM API instead of GitHub Issues. Full relational model with browser UI if wanted. The existing capture pipeline (email-sync, transcript-sync) reroutes to CRM instead of GitHub. Conversion event: agent creates GitHub milestone from CRM record.
*Limitation:* New tool to learn. Capture pipeline needs rerouting. Higher operational complexity.

#### Recommendation: A → C → B progression

Start with Architecture A. The agent is the highest-value, lowest-risk first deliverable — it solves the behavioral problem ("we see but don't act") regardless of data layer. Build the agent on GitHub, where everything already works.

When the relational gap proves limiting in practice — when Marius asks "who else is at Pöckemöller's company?" and the answer requires manual searching — graduate to Architecture C. The agent adds a lightweight relational index. No new tools for the team.

If the team or lead volume grows to the point where a browser dashboard, contact enrichment, or full CRM workflows become necessary, graduate to Architecture B. At that point, Attio (strongest AI integration via official MCP) and Twenty (strongest open-source/self-host story) are the two candidates for POC evaluation.

**Undefined:** Specific graduation triggers — what signals indicate Architecture A is insufficient and it's time to move to C or B. This will be informed by real-world usage of the agent on GitHub.

**Undefined:** Final CRM selection for Architecture B — Twenty vs Attio. Depends on POC evaluation when graduation is triggered. Twenty leads on open-source/self-hosting. Attio leads on AI integration (official MCP, OpenAPI, free tier covering 3 seats).

### Part 2: Lead Lifecycle — From Reactive to Defined Stages

Today leads have no lifecycle. They sit in GitHub Issues until an external trigger (lead reaches out) or Marius manually engages. There are no defined stages, no transition triggers, and no visibility into pipeline health.

**Proposed lifecycle:**

```
New → Qualified → Engaged → Converted | Dead
```

| Stage | Entry trigger | Exit trigger | Owner |
|-------|--------------|--------------|-------|
| **New** | Lead created (from email, transcript, meeting, referral) | Marius reviews and qualifies | Auto (system) |
| **Qualified** | Marius confirms: real opportunity, worth pursuing | First substantive engagement (meeting booked, proposal sent) | Marius |
| **Engaged** | Active conversation — meetings, proposals, design docs | Deal signed (converted) or lead goes cold (dead) | Marius + David |
| **Converted** | Contract signed → lead becomes a client deliverable | N/A — exits lead system, enters deliverable-tracking | Marius |
| **Dead** | Lead explicitly rejected, went cold, or lost to competitor | Can be reopened if lead re-engages | Marius |

**Integration with deliverable-tracking:**
- **Converted** leads create a new client label + milestone in deliverable-tracking
- The lead record in the CRM links to the deliverable-tracking milestone
- Pre-conversion work (design docs, proposals) stays in the CRM as lead history
- Post-conversion work (implementation, delivery) lives in GitHub Issues

**Undefined:** Transition triggers need hands-on testing with real leads to validate. The stage names and boundaries above are starting points — expect refinement after the first month of use.

### Part 3: Lead Deduplication — One Artifact Per Lead

The current system allows leads to fragment across multiple GitHub artifacts:
- Stafford: #874 (epic, now closed), #828 (lead issue), #1078 (design doc)
- Omega: #1071 (epic), #1076 (design doc), #1247 (POC)
- Kotalik: #657 and #778 (duplicate lead issues)

**Rule: One canonical record per lead in the CRM tool.** Design docs, proposals, and meeting notes attach to that record — not as separate issues.

**Migration approach:**
1. Each existing lead gets ONE record in the new CRM
2. Related GitHub issues (epics, design docs, POC issues) become linked references on the CRM record, not standalone tracking artifacts
3. When a lead converts to a client, the CRM record links to the new deliverable-tracking milestone — clean handoff point
4. Existing `lead`-labeled GitHub issues are closed with a comment pointing to the CRM record ("Migrated to Lead System: [link]")

**Prevention:**
- Lead creation happens in the CRM tool only (via CLI or MCP)
- GitHub Issues stops being used for lead tracking entirely
- Design docs for leads live as attachments/notes on the CRM record
- The `lead` label in deliverable-tracking becomes deprecated

### Part 4: Label & Milestone Alignment — Deprecation Path

The `lead` label and `[SALES-PIPELINE] Active Leads` milestone in deliverable-tracking have zero overlap (16 issues each, none shared). Neither functions as intended:

- `lead` label = raw inbound leads, mostly closed, no milestone
- Active Leads milestone = engaged pipeline work, uses `09_SALES-PIPELINE` + `ja/design` labels instead

This is not two lifecycle stages that accidentally emerged — it's pure drift from inconsistent usage over time.

**Resolution:** Both mechanisms are deprecated once the CRM tool is operational.

| Current mechanism | Action |
|-------------------|--------|
| `lead` label | Deprecate. Close remaining open issues with migration comment. |
| `[SALES-PIPELINE] Active Leads` milestone | Deprecate. Active items migrate to CRM pipeline stages. |
| `09_SALES-PIPELINE` label | Retain for deliverable-tracking items that are post-conversion (active client work from sales origin). |

**Transition period:**
- New leads go directly into the CRM tool (not GitHub Issues)
- Existing open leads in either system get migrated to CRM records
- The `lead` label is removed from deliverable-tracking's active label set
- The Active Leads milestone is closed after migration

### Part 5: Lead vs Deliverable Boundary — When Pre-Sales Becomes a Project

Some items in the Active Leads milestone have already crossed into delivery work (e.g., Omega #1247 with `maker/implement` label). The boundary between lead and deliverable is undefined, causing scope confusion.

**Boundary rule: Contract signature = conversion point.**

| Phase | System | Artifacts |
|-------|--------|-----------|
| **Pre-conversion** (lead) | CRM tool | Lead record, meeting notes, design docs, proposals |
| **Conversion event** | Both | CRM record marked "Converted" → new client label + milestone in deliverable-tracking |
| **Post-conversion** (deliverable) | GitHub Issues | Implementation issues, sprints, milestones, client label |

**What moves and what stays:**
- The CRM lead record stays in the CRM (historical record, never deleted)
- A new client label is created in deliverable-tracking (e.g., `10_NEWCLIENT`)
- A gatekeeper milestone is created for the contract
- Implementation work is created as GitHub Issues under that milestone
- Design docs produced during pre-sales stay linked from both systems

**Edge case — POC before contract:**
POC work (like Omega #1247) happens during pre-sales but involves implementation. These stay in the CRM as "Engaged" stage activities. Only when a full contract is signed does the lead convert and implementation work move to deliverable-tracking.

**Undefined:** The exact handoff automation (CRM → GitHub Issues) depends on which tool is selected in Part 1. Twenty's workflow engine or Attio's automations could trigger the creation of deliverable-tracking artifacts.

---

## Source

- **Transcript:** [Grooming 2026-03-30 (Marius + David)](https://app.fireflies.ai/view/01KMZBXEW07KHCHV9X8QTFP2HT) — CLI vs MCP, "GitHub of CRM", Pipedrive reference, design pass approach, marketing department entry point
- **Issue:** [#1336 Lead System Agent — Position Epic](https://github.com/DaveX2001/deliverable-tracking/issues/1336) — 5 scope areas, observation from Marius on artifact duplication
- **Research:** Deep dives on Twenty CRM, Attio, HubSpot CLI, crm-cli (4 parallel agents, March 2026)
- **Data:** `lead` label query (16 issues) + `Active Leads` milestone query (16 issues) — zero overlap confirmed
- **Session:** 764ddbc2-9bc8-4655-a8f4-bbda0f2a9351
- **Feedback:** [Marius comment — GitHub vs CRM comparison request](https://github.com/DaveX2001/deliverable-tracking/issues/1336#issuecomment-4167229026), [Part 2 needs product-line pass](https://github.com/DaveX2001/deliverable-tracking/issues/1336#issuecomment-4167231574), [Focus order](https://github.com/DaveX2001/deliverable-tracking/issues/1336#issuecomment-4167233205), [UWI Part 3 as lead tracking example](https://github.com/DaveX2001/deliverable-tracking/issues/1336#issuecomment-4167360268)
- **Research (Pass 2):** GitHub Projects v2 documentation, Twenty CRM docs + `/llms.txt` + CLI + MCP ecosystem, Attio CRM docs + `/llms.txt` + OpenAPI + official MCP (3 parallel agents, April 2026)
- **Evidence:** [UWI Design Doc — Ulrich Sync 2026-03-30, Part 3: FSO-Seminar Follow-up](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-ulrich-sync-2026-03-30) — 4 contacts as concrete test case for lead tracking
- **Session (Pass 2):** b7dd95c0-be39-420f-a383-f6565350dab7

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

