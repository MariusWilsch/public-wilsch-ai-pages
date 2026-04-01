---
publish: true
---

# Post-Demo Landscape — Multi-Entity Expansion & Conditional Imports
[[client-archibus]]

Gap analysis of the Asset demo pipeline against the full background data import vision. Maps three work streams, per-entity requirements, and undefined elements for next-phase decomposition.

---

## Problem Statement

The Asset demo (March 24-25, 2026) proved the AI bulk data entry pipeline end-to-end: hierarchy inference → column mapping → enum resolution → contract assembly → API import with backpressure correction. Rein confirmed data integrity in BruceBEM. Miguel wants to demo this beyond Assets.

But Assets is one of 12+ background data tables. The pipeline — MCP tools, contract schema, skills, data references — is built for Assets only. Three work streams exist to expand:

1. **Conditional imports** — the current pipeline assumes an empty database. Production databases have existing data. Before inserting, the system needs to find whether entities already exist and handle conflicts.
2. **Multi-entity expansion** — extending the composable pipeline pattern to other BEM tables (Departments, Employees, Partners, Works, PM Schedules). Each entity type needs its own schema references, API endpoints (from Rein), and AI pipeline adaptation.
3. **Finishing the PoC** — known debt from the Asset demo: session-scoped directories, progressive disclosure migration, cost reduction, enum cleanups, BEM context quality.

These work streams are not independent. Conditional imports compound with multi-entity expansion (clean Employees import ≠ conditional Employees import). Finishing the PoC is foundational — scaling a brittle pipeline produces brittle scale.

**Preconditions:**
- Asset pipeline proved E2E (demo March 24-25, 2026)
- Rein's Action Plan Stage 3 scopes ~25 days of API work across 5 entity types
- Ryan meeting (March 31, 2026) is the coordination point for API ↔ AI alignment

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Landscape map of the three work streams with per-entity analysis — what exists, what's needed from API (Rein) and AI (Marius), what's undefined |
| **Success** | Ryan meeting produces aligned understanding of what's on the table. Priority ordering and entity sequencing emerge from the meeting, not from this document. |
| **Done test** | Can we create concrete implementation issues per entity type with API dependencies explicit? → If YES → landscape is complete |

## Approach

### Part 1: What's Built — Asset Pipeline Inventory

The demo validated a complete pipeline for Assets. This is the baseline the other work streams build on.

**Pipeline stages:** parse → hierarchy → generate → build-json → push. Each stage is a module in `src/archibus_bulk_import/`. The `run_pipeline()` function chains them for one building at a time.

**MCP tools (4):**

| Tool | Purpose | Entity-specific? |
|------|---------|-----------------|
| `bem_context` | Serves BEM schema references (fields, asset types, status, hierarchy, countries) | Yes — Asset fields only |
| `excel_analysis` | Profiles uploaded Excel (columns, types, sample values) | No — generic |
| `save_contract` | Validates + persists mapping contract (Pydantic) | Partially — contract schema is entity-agnostic, but validation references Asset fields |
| `run_import` | Bridge tool: contract + Excel → API submission | Yes — calls Asset import endpoints |

**Skills (2):**

| Skill | Mode | Status |
|-------|------|--------|
| `bem-setup` | Interactive (Steps 0→2) | Working — drove the demo |
| `bem-backpressure` | Autonomous (Step 3) | Minimum viable — needs production content |

**Contract schema (Pydantic):** Three elements — `hierarchy` (ordered levels), `fieldMappings` (column → BEM field), `enumRules` (value translation tables). The schema itself is entity-agnostic. The content is Asset-specific.

**What transfers to other entities:** `excel_analysis` (generic), contract schema structure (3 elements), pipeline stages (parse, generate, build-json, push), backpressure loop pattern. **What doesn't transfer:** `bem_context` data, `run_import` API endpoints, hierarchy inference (not all entities have hierarchy), skill content (Asset-specific flows).

### Part 2: Per-Entity Landscape

Each BEM background data entity requires work on two sides: Rein's API (endpoints, schemas, enums) and the AI pipeline (context data, contract adaptation, skill content). The entities are ordered by structural complexity, not priority — priority comes from the Ryan meeting.

**Import dependency order:** Independent entities must exist before dependent ones can reference them. This is the natural implementation sequence regardless of priority.

```
Layer 1 (no dependencies):  Partners, BusinessUnits
Layer 2 (references L1):    Departments (→ BusinessUnit)
Layer 3 (references L2):    Employees (→ Department, BusinessUnit)
Layer 4 (references L1-3):  Assets (→ hierarchy + optionally Employees)
Layer 5 (references L3-4):  Works (→ Asset + Employee), PM Schedules (→ Asset)
```

| Entity | Hierarchy | Fields (est.) | Enums | API (Rein) | AI adaptation | New challenge |
|--------|-----------|---------------|-------|------------|---------------|---------------|
| **Assets** | 9 levels | 35 | High (types, status, condition) | ✅ Exists | ✅ Done | — |
| **Partners** | None | Few (name, address, regCode) | Country | In Action Plan (Step 2+3) | Low — flat mapping, no hierarchy stage | Simplest extension |
| **BusinessUnits** | None | Few (name, description) | Minimal | In Action Plan (Step 2+3) | Low — flat mapping | — |
| **Departments** | 1-level (→ BU parent) | Few | Minimal | In Action Plan (Step 2+3) | Low — simpler hierarchy than Assets | — |
| **Employees** | None (placement into Dept) | Medium (name, code, profession) | Profession? | In Action Plan (Step 2+3) | Medium — foreign key to Department | Cross-entity reference resolution |
| **Works** | None | Medium (description, dates, status) | Status, priority | In Action Plan (Step 2+3) | High — references both Asset and Employee | Multi-entity foreign keys |
| **PM Schedules** | None | Medium (procedures, steps) | Frequency? | In Action Plan (Step 2+3) | Medium — references Asset, has sub-entity (PM Steps) | Parent-child within same import |

**Rein's Action Plan — API side (25 days total):**

| Step | Days | What | Work Stream |
|------|------|------|-------------|
| 1. Search/lookup | 7 | Find entities by name/code — prerequisite for conditional imports | WS1 (Conditional) |
| 2. Clean import | 5 | Insert into empty database — Partners, Employees/Depts, Works, PM Schedules | WS2 (Multi-entity) |
| 3. Conditional import | 8 | Check-then-insert — same entities but with existence checks | WS1 (Conditional) |
| Meetings + docs | 5 | Coordination with Marius | Both |

### Part 3: Finishing the PoC

Known debt from the Asset demo. These are items that worked for a single-user demo but need resolution before scaling to more entity types or production usage.

| Item | Current state | What's needed | Blocks |
|------|--------------|---------------|--------|
| **Session-scoped directories** | `save_contract` writes to `/app/uploads/contract.json` — single path, last write wins | Per-session paths (`/app/uploads/{session_id}/contracts/`) per §6.3.1 of Setup Phase design | Multi-client, multi-session usage |
| **Progressive disclosure** | 14KB system prompt baked into LibreChat agent. Works for Claude Sonnet (large context) | Metadata nudge + `read_resource` discovery per §6.2 — required for Qwen 3.5 (9B production target) | Smaller model deployment, cost reduction |
| **Cost** | ~$40/2hrs on OpenRouter with current approach | Smaller model path (Qwen 3.5 9B) — depends on progressive disclosure migration | Production viability |
| **BEM context quality** | `bem_context` serves Asset schema from `data/*.xlsx`. Field descriptions and enum values are from SharePoint snapshots | Review by Rein/Ali/Mujahid — are field descriptions accurate? Are enum values current? | Multi-entity expansion (each entity needs its own quality-reviewed context) |
| **Condition enum** | Placeholder in golden contract. Design doc marked "pending Rein's API schema delivery" | Rein delivers finalized condition enum values | Asset enum completeness |
| **Legacy enum cleanup** | Codebase uses old AssetType IDs (`AssetTypeOld` sheet). Per Rein discussion, only New FRA types should remain | Confirm with Rein which enum set is authoritative, then update `data/asset_types.xlsx` and `bem_context` | Correct enum mappings |
| **Backpressure skill content** | `bem-backpressure/SKILL.md` exists with minimum viable 4-step loop | Production content: nuanced escalation, multi-error handling, contract versioning | Robustness under varied client data |
| **Additional properties** | API endpoint ✅ implemented by Rein | AI pipeline + contract schema need a 4th element to support unmapped columns → `additionalProperties` key-value pairs | Complete column coverage |

**Undefined:** Per-entity schema details (field counts, required/optional, enum definitions), API endpoint signatures, and foreign key resolution mechanisms are all undefined — they require Rein's input. See [Ryan Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/ryan-meeting-agenda-multi-entity-api-alignment).

---

## Source

- **Design docs:**
  - [Step 1+2: Setup Phase Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design)
  - [Chain 1B: Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
  - [Step 3: Fill Level-by-Level](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
- **Data artifacts:**
  - [Action Plan Stage 3 — Rein (BruceBEM)](https://docs.google.com/spreadsheets/d/1e38WCNAFgs2VPY7jMtLncrCUW1N0wu8S/edit)
  - [background-data-tables.xlsx](https://docs.google.com/spreadsheets/d/1rNv4fDUMS7ve8ftbiZUiKbGebLesM54m/edit)
  - [AssetImportDescription (36 fields)](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit)
- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852) — Design Step 1+2 Setup Phase
- **Parent epic:** [#373](https://github.com/DaveX2001/deliverable-tracking/issues/373) — AI Bulk Data Entry
- **Project Index:** [#373](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/transcript-index-373)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-01-ARCHIBUS--archibus-bulk-import/25a2847c-45f0-4b02-ae8b-43d5bbfd9539.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

