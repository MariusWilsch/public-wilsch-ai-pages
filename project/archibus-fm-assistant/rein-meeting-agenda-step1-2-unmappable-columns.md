---
publish: true
---

# Meeting Agenda: Step 1+2 Setup Phase — Unmappable Column Handling
[[client-archibus]]

## Meeting Goal

Resolve one open design question from the Step 1+2 design doc: what happens to client columns that have no match in BEM's 35 schema fields.

1. **Unmappable column handling** — disposition path for data that doesn't fit BEM's schema

## Pre-Read

[Step 1+2 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design) — see Part 2, Undefined marker.

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Unmappable Column Handling
⏱️ 15 min

When the AI maps client columns to BEM's 35 fields, some columns won't match anything — "maintenance frequency", "assigned teams", or domain-specific fields that BEM doesn't have.

- Current design: binary mapping (mapped / flagged)
- Flagged columns reach the implementer, but their disposition is undefined
- The implementer needs to know: drop it, force-map to closest field, or store somewhere
- BEM's `additionalProperties: false` on the import schema means extra fields can't just pass through

**To resolve:** The disposition path for client columns that have no BEM field match — whether BEM has a catch-all mechanism or the data is acknowledged and excluded.

## Meeting Format

- **Type:** Technical alignment
- **Attendees:** Rein, Marius
- **Expectation:** Rein brings knowledge of BEM's import API constraints
- **Outcome:** Clear disposition path written into Step 1+2 design doc

## Related

- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852)
- **Design Doc:** [Step 1+2 Setup Phase](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design)
- **Parent:** [Chain 1B: Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
