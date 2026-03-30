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

### Part 1: Tooling Fit — CLI-Native Lead Management Platform

The current system (GitHub Issues with `lead` label + `Active Leads` milestone) lacks CRM primitives. A separate, purpose-built tool is needed — one that is CLI-native (operable from terminal) and AI-native (structured data accessible to Claude Code).

**Requirements:**
1. **CLI-first** — create, update, query, and move leads from terminal (not just "has an API")
2. **AI-native** — structured data model + MCP or agent-readable output for Claude Code reasoning
3. **Pipeline management** — visual/queryable lead stages with customizable lifecycle
4. **Preferably open source and self-hostable** (nice-to-have, not hard constraint)

**Evaluated tools:**

| Tool | CLI | MCP | Open Source | Self-host | Pipeline | Fit |
|------|-----|-----|-------------|-----------|----------|-----|
| **Twenty CRM** | `twenty-cli` (community, `agent` output format, pending official merge) | 3 community implementations (25 tools) | AGPL-3.0, 43k stars | Docker Compose, $12-20/mo VPS | Kanban, custom stages | ★★★★ |
| **Attio** | `attio-cli` (community, 10 stars) + curl | Official hosted MCP at `mcp.attio.com` (35 tools) | Closed source | Cloud only | Kanban, deals object | ★★★☆ |
| **HubSpot + hubspot-cli** | `hubspot-cli` (community, dual CLI+MCP binary) | Same binary serves as MCP server | Closed source | Cloud only | Mature (1 pipeline on free) | ★★☆☆ |
| **crm-cli** | Terminal-only (IS the product) | None | Open source, 72 stars | Local JSON | No pipeline | ★☆☆☆ |

**Twenty CRM** is the strongest match:
- Open source (AGPL-3.0), 43k GitHub stars, 604 contributors, v1.19.0 (March 2026)
- CLI has `--help-json` for agent discovery and `agent` output format for AI pipelines
- Self-hostable via Docker Compose on a $12-20/month VPS (2 GB RAM minimum)
- Custom objects supported — can define a "Lead" object if the default People+Opportunities model doesn't fit
- GraphQL + REST APIs, community MCP servers for Claude integration

**Attio** is the strongest AI integration:
- Official first-party MCP server (rare — almost no CRM offers this)
- Object-relational data model excellent for structured AI queries
- Free tier covers 3 seats with 50k records
- Risk: closed source, 3 custom objects on free tier, enriched data doesn't export

**Undefined:** Final tool selection depends on hands-on evaluation. Twenty and Attio are the two candidates for deeper POC testing.

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

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

