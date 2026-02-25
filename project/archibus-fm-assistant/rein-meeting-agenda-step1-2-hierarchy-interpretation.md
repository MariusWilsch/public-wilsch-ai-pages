---
publish: true
---

# Meeting Agenda: Step 1+2 Setup Phase — Hierarchy & Interpretation
[[client-archibus]]

## Meeting Goal

Resolve five open design questions from the Step 1+2 design doc — AI interpretation behaviors for hierarchy inference, naming, and data handling.

1. **Unmappable column handling** — disposition path for unmatched columns
2. **Hierarchy type descriptions** — definitions per BEM AssetType for AI inference
3. **Geographic inheritance** — child assets inheriting address from container
4. **Rule discrimination** — when AI enriches vs passes through raw data
5. **Readability validation** — AI flagging unreadable asset names

## Pre-Read

[Step 1+2 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design) — see Part 1 (hierarchy inference examples, proposed type descriptions) and Part 2 (unmappable columns).

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

### 2. Hierarchy Type Descriptions per AssetType
⏱️ 15 min

BEM's asset_types file defines 9 hierarchy levels with only a name and depth code. The AI needs descriptions and examples per type to distinguish Building from Complex when client data uses domain-specific naming.

- Current state: asset_types has `id`, `show`, `hierarchy` (A-code depth) — no descriptions
- The AssetImportDescription Fields Assets sheet has descriptions per field — same format needed per type
- Proposed descriptions with examples are in the [Step 1+2 Design Doc — Hierarchy Type Descriptions](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design)
- The Housekeeping Tracker walkthrough in the same doc shows why "CONCOURSE A" can't be mapped from the word alone

**To resolve:** Whether BEM can provide or validate descriptions and examples per hierarchy AssetType, following the same format as the field descriptions in AssetImportDescription.

### 3. Geographic Inheritance for Sub-Elements
⏱️ 10 min

If a building has an address, should its floors and rooms also carry that address — or does BEM read it from the parent?

- CAFM example: Building A has "Exeter rd 25". Floor 1 and Room 194 are children. Do they need the address repeated?
- Miguel's position: only the top-level container carries the address, children inherit

**To resolve:** Whether sub-elements need explicit address fields or inherit from their container in BEM's data model.

### 4. Name Enrichment — When to Apply
⏱️ 10 min

The AI enriches names when raw data is unreadable: "3" becomes "Building D Floor 3". But if data is already readable ("CONCOURSE C"), it passes through unchanged.

- CAFM: "Building D" → pass through. "3" (floor) → enrich
- Housekeeping: "CONCOURSE C" → pass through. "PTC" → flag (unclear)

**To resolve:** Whether BEM has naming conventions that inform where the enrichment boundary sits.

### 5. Readability Validation for Asset Names
⏱️ 10 min

Should the AI flag asset names that no stakeholder could understand — like a building named "X1" or an equipment asset named "ABC123"?

- Miguel (Feb 23): names should be human-readable, not machine codes
- Open: does this check happen in Step 1 (interactive) or Step 3 (backpressure)?

**To resolve:** Whether readability validation is a BEM concern (API-enforced) or an AI concern (flagged during setup).

## Meeting Format

- **Type:** Technical alignment
- **Attendees:** Rein, Marius
- **Expectation:** Rein brings knowledge of BEM's import API constraints
- **Outcome:** Clear disposition path written into Step 1+2 design doc

## Related

- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852)
- **Design Doc:** [Step 1+2 Setup Phase](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design)
- **Parent:** [Chain 1B: Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
