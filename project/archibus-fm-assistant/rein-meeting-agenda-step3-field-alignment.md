---
publish: true
---

# Meeting Agenda: Step 3 — Field Alignment & Edge Cases
[[client-archibus]]

## Meeting Goal

Resolve three undefined areas in the Step 3 design to move them from Partially Defined → Defined.

1. **Field naming clarity** — which naming scheme the insertion API expects
2. **Country mapping responsibility** — who resolves country name → country ID
3. **Floor All placement** — where building-wide equipment attaches in the hierarchy

## Pre-Read

[Chain 1B: Step 3 Fill Level-by-Level — Design Document](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)

Focus on Approach sections 4 and 5 (marked ⚠️ Partially Defined).

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Two naming schemes exist for the same fields

The AssetImportDescription has ImportTemplate names in PascalCase (`Name`, `AssetType`, `ParentId`). The Hierarchical Body JSON example uses lowercase (`name`, `type`, `parent_id`). The current import app expects ImportTemplate names. The new insertion API may use different names since it's being built from scratch.

We need to align on which field names the JSON should use so AI generates the correct output.

### 2. Country values in Excel are names, Bruce BEM stores IDs

Row 18 of the AssetImportDescription notes: "There is a problem, because Bruce has the country IDs, but in Excel normally Country Names." When AI encounters "Germany" in a client's Country column, something needs to translate that to Bruce BEM's internal country ID.

The question is whether the insertion API handles this translation, or whether AI needs a lookup table.

### 3. Some equipment serves an entire building, not a specific floor

In the CAFM sample data, 17% of equipment rows have Floor = "All" (elevators, fire systems, plumbing). These are building-wide assets. The current design attaches equipment to the lowest known hierarchy level — but "All" is not a specific floor.

The equipment still has a Room number, which might represent the access point or control room rather than the equipment's actual location.

## Meeting Format

- **Type:** Technical review
- **Expectation:** Rein brings clarity on the insertion API's expected field format
- **Outcome:** All three topics resolved → Step 3 design doc updated to Defined

## Related

- **Issue:** [#373 — ARCHIBUS: AI Bulk Data Entry Prototype](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Design Doc:** [Chain 1B: Step 3 Fill Level-by-Level](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
