---
publish: true
---

# Ryan Meeting — Multi-Entity API Alignment
[[client-archibus]]

## Meeting Goal

Align the AI pipeline roadmap with Rein's API Action Plan Stage 3 — entity by entity. Two outcomes:

1. **Entity priority** — which background data tables to tackle next, based on commercial demo value and API readiness
2. **Work stream sequencing** — does Rein build clean import endpoints first (enabling multi-entity), or search/lookup first (enabling conditional imports), and how does the AI pipeline track that sequence?

## Pre-Read

- [Post-Demo Landscape](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/post-demo-multi-entity-landscape) — gap analysis, per-entity table, three work streams
- [Action Plan Stage 3 — Rein](https://docs.google.com/spreadsheets/d/1e38WCNAFgs2VPY7jMtLncrCUW1N0wu8S/edit) — Rein's 25-day API scope (search/lookup, clean import, conditional import)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The Asset demo proved one table — Miguel wants to demo more
⏱️ 10 min

The Asset pipeline handles hierarchy inference, column mapping, enum resolution, and backpressure correction. It works end-to-end. But BEM has 12+ background data tables, and Miguel's reaction was "this is cool, I want to demo it" — implying he wants the broader story.

- Rein's Action Plan lists 5 entity types: Assets, Employees, Departments, Partners, Works, PM Schedules
- The AI pipeline is composable — contract schema, pipeline stages, and backpressure loop transfer. Entity-specific parts (schema references, API endpoints, skill content) need per-entity work.
- Import dependencies create a natural layer order: Partners/BusinessUnits first → Departments → Employees → Assets → Works/PM Schedules

**To resolve:** Which entity types carry the most commercial demo value beyond Assets, and in what sequence should Rein's API endpoints and the AI pipeline be built.

### 2. Rein's Action Plan weights toward conditional imports — the AI pipeline only knows clean insert
⏱️ 10 min

Two of Rein's three steps (15 of 20 dev days) are about handling existing data: search/lookup endpoints (Step 1, 7 days) and conditional imports (Step 3, 8 days). Only 5 days are on clean import for new entity types.

- **Clean import (5 days):** Add new background tables (Employees, Departments, Partners, Works, PM Schedules) into an empty database — extending the proven Asset pattern to more entity types
- **Search/lookup (7 days):** Make existing background tables queryable — the AI can check "does this Employee already exist?" before inserting. Prerequisite for conditional imports.
- **Conditional import (8 days):** Check-then-insert — the AI queries first, then decides to insert, skip, or update. The current pipeline has no concept of this.

**To resolve:** Whether Rein prioritizes clean import endpoints (new entity types sooner) or search/lookup first (existing data handling), and what the AI pipeline needs from each.

### 3. Cross-entity references — Employees need Departments, Works need Assets
⏱️ 10 min

The Asset pipeline is self-contained: hierarchy levels reference each other (Building → Floor → Room), but no external entities. Other entity types introduce cross-table foreign keys.

- Importing an Employee requires a reference to an existing Department (UUID). The client's Excel says "Sales Department" — the pipeline needs to resolve that to BEM's internal ID.
- Works reference both Assets and Employees. PM Schedules reference Assets.
- Rein's search/lookup endpoints (Action Plan Step 1) may provide the resolution mechanism — query by name, get UUID back.
- The AI pipeline's mapping contract currently has no element for cross-entity references. The contract schema may need a 4th element, or foreign keys may be handled as a special case in `fieldMappings`.

**To resolve:** How cross-entity references are resolved at the API level — does the AI pass a name and the API resolves to UUID, or does the AI need to query first and pass the UUID directly?

## Meeting Format

- **Type:** Discovery
- **Expectation:** Ryan has reviewed Rein's Action Plan and the per-entity landscape table from the Pre-Read
- **Outcome:** Agreed entity priority order + work stream sequencing decision (clean import first vs. search/lookup first)

## Related

- **Design doc:** [Post-Demo Landscape — Multi-Entity Expansion & Conditional Imports](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/post-demo-multi-entity-landscape)
- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852) — Design Step 1+2 Setup Phase
- **Data artifact:** [Action Plan Stage 3 — Rein](https://docs.google.com/spreadsheets/d/1e38WCNAFgs2VPY7jMtLncrCUW1N0wu8S/edit)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-01-ARCHIBUS--archibus-bulk-import/25a2847c-45f0-4b02-ae8b-43d5bbfd9539.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

