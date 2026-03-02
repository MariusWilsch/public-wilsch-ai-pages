---
publish: true
---

# Meeting Agenda: Step 1+2 Setup Phase — Hierarchy & Interpretation
[[client-archibus]]

## Meeting Goal

Two remaining items from the original 8-topic agenda. Topics 1-5 and 8 resolved (Feb 27 2026).

1. **Additional properties API schema** — Rein's deliverable: updated asset import body with key-value array
2. **Location-only imports** — deferred to Ali meeting for data clarification

## Pre-Read

[Step 1+2 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design) — Part 1 (hierarchy inference, type descriptions), Part 2 (column taxonomy, enum resolution), Part 3 (contract elements, data type detection).

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Additional Properties API Schema
⏱️ 15 min

Rein is adding an `additionalProperties` array to the asset import body — key-value pairs per asset for unmapped columns, replacing the statusDetail overflow approach. The mapping contract needs a 4th element once the API schema is finalized.

- Rein's deliverable: updated asset schema with additionalProperties array (~week of March 3, 2026)
- Each asset node gets an optional array: `[{key: "columnName", value: "cellValue"}, ...]`
- Empty array if no unmapped columns
- API inserts key-value pairs into separate BEM table with foreign key to asset

**To resolve:** The exact JSON structure of the additionalProperties array in the import body, and how the mapping contract records which columns route to additional properties vs exclude.

### 2. Location-Only Imports + Ali Data Clarification
⏱️ 10 min

Ali submitted an Excel that neither Rein nor Marius could interpret — appears to be work requests or employees, not asset data. A meeting with Ali is needed to clarify what the data contains and whether it's usable as a second test dataset.

- Housekeeping Tracker also had no assets (confirmed by Rein)
- Location-only import (hierarchy without equipment) remains an open design question
- Need better test examples from the FMM team — Rein will escalate via Miguel

**To resolve:** Whether Ali's data contains importable assets, and whether location-only imports are a valid Phase 1 scenario.

## Meeting Format

- **Type:** Technical alignment
- **Attendees:** Rein, Marius, Ali (for topic 2)
- **Expectation:** Rein brings knowledge of BEM's import API constraints
- **Outcome:** Resolved Undefined markers in the Step 1+2 design doc — hierarchy, mapping contract, and enum sections

## Related

- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852)
- **Design Doc:** [Step 1+2 Setup Phase](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design)
- **Parent:** [Chain 1B: Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
