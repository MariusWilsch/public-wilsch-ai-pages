---
publish: true
---

# Meeting Agenda: Step 1+2 Setup Phase — Hierarchy & Interpretation
[[client-archibus]]

## Meeting Goal

Resolve nine open design questions from the Step 1+2 design doc — covering hierarchy inference, naming, column mapping, enum resolution, and import pipeline scope.

**Pass 1 topics (hierarchy & interpretation):**
1. **Hierarchy type descriptions** — definitions per BEM AssetType for AI inference
2. **Geographic inheritance** — child assets inheriting address from container
3. **Rule discrimination** — when AI enriches vs passes through raw data

**Pass 2 topics (mapping contract & enums):**
4. **Import API field expansion** — condition and maintenance fields in import endpoint
5. **Status enum mapping** — ambiguous values like "Inactive" → which BEM status?
6. **Unmappable column disposition** — overflow vs exclude, catch-all mechanisms
7. **assignedPortfolioEmployee timeline** — when Skip → AI tier?
8. **Location-only imports** — hierarchy without equipment as valid scenario
9. **Background data entity prioritization** — which entity after Assets?

## Pre-Read

[Step 1+2 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design) — Part 1 (hierarchy inference, type descriptions), Part 2 (column taxonomy, enum resolution), Part 3 (contract elements, data type detection).

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Hierarchy Type Descriptions per AssetType
⏱️ 15 min

BEM's asset_types file defines 9 hierarchy levels with only a name and depth code. The AI needs descriptions and examples per type to distinguish Building from Complex when client data uses domain-specific naming.

- Current state: asset_types has `id`, `show`, `hierarchy` (A-code depth) — no descriptions
- The AssetImportDescription Fields Assets sheet has descriptions per field — same format needed per type
- Proposed descriptions with examples are in the [Step 1+2 Design Doc — Hierarchy Type Descriptions](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design)
- The Housekeeping Tracker walkthrough in the same doc shows why "CONCOURSE A" can't be mapped from the word alone

**To resolve:** Whether BEM can provide or validate descriptions and examples per hierarchy AssetType, following the same format as the field descriptions in AssetImportDescription.

### 2. Geographic Inheritance for Sub-Elements
⏱️ 10 min

If a building has an address, should its floors and rooms also carry that address — or does BEM read it from the parent?

- CAFM example: Building A has "Exeter rd 25". Floor 1 and Room 194 are children. Do they need the address repeated?
- Miguel's position: only the top-level container carries the address, children inherit

**To resolve:** Whether sub-elements need explicit address fields or inherit from their container in BEM's data model.

### 3. Name Enrichment — When to Apply
⏱️ 10 min

The AI enriches names when raw data is unreadable: "3" becomes "Building D Floor 3". But if data is already readable ("CONCOURSE C"), it passes through unchanged.

- CAFM: "Building D" → pass through. "3" (floor) → enrich
- Housekeeping: "CONCOURSE C" → pass through. "PTC" → flag (unclear)

**To resolve:** Whether BEM has naming conventions that inform where the enrichment boundary sits.

### 4. Import API Field Expansion for Condition and Maintenance
⏱️ 10 min

BEM's full asset table has `condition` (nvarchar 50) and `classification_for_maintenance` (nvarchar 128), but the import API's 36-field subset doesn't include them. Client data commonly contains condition assessments and maintenance schedules.

- CAFM sample: "Condition" column (Excellent/Good/Fair/Poor/New) — currently unmappable via import API
- CAFM sample: "Maintenance Frequency" column (Monthly/Quarterly) — currently unmappable
- Both fields exist in the database, just not exposed via the import endpoint

**To resolve:** Whether `condition` and `classification_for_maintenance` can be added to the import API, making common client data columns directly mappable.

### 5. Status Enum Semantic Mapping
⏱️ 10 min

Rein merged AssetStatus and Status into a single Status field (Feb 19). Client data uses generic values ("Active", "Inactive") that don't always match BEM's 26 predefined statuses. "Active" matches directly; "Inactive" has no exact equivalent.

- Closest candidates for "Inactive": "Out of Service", "Not Ready", "In Storage"
- The AI proposes ranked candidates during Step 2; the implementer picks
- Establishing standard mappings for common generic values would reduce per-client effort

**To resolve:** Rein's intended semantic mapping for common generic status values that don't have exact BEM equivalents.

### 6. Unmappable Column Disposition
⏱️ 10 min

When a client column has no BEM field match, the AI flags it neutrally and the implementer chooses: overflow to statusDetail (pipe-separated catch-all) or exclude.

- statusDetail is the only overflow field (2000 chars, pipe-separated)
- Some unmappable columns carry valuable data (e.g., Manufacturer before brandSpecific was added Feb 19)
- BEM's `additionalProperties: false` on the import schema means extra fields can't pass through
- The AI presents options without recommending — the implementer owns the decision

**To resolve:** Whether statusDetail overflow is the intended disposition path for unmappable-but-valuable columns, or whether BEM has other mechanisms.

### 7. assignedPortfolioEmployee Timeline
⏱️ 5 min

Currently Skip tier — requires Guid lookup against an employee list. The AssetImportDescription notes "(For next version)." Client data commonly includes "Assigned To" columns with person names.

- Employee lookup adds complexity (name → Guid resolution)
- Currently excluded from the mapping contract entirely

**To resolve:** Timeline and prerequisites for moving assignedPortfolioEmployee from Skip to AI tier.

### 8. Location-Only Imports
⏱️ 10 min

Not all client data is asset inventories. Operational trackers (Housekeeping Tracker, maintenance logs) contain location hierarchies but no equipment rows. The AI detects this and surfaces it to the implementer before column mapping begins.

- Housekeeping Tracker (FMM Qatar): 25 columns of work order data, only 3 are BEM-relevant location columns (AREA, SUB AREA, LOCATION)
- Extracting the location tree without equipment could pre-populate BEM's hierarchy for a site

**To resolve:** Whether the import pipeline should accept location-only data (hierarchy without equipment children) as a valid import scenario.

### 9. Background Data Entity Prioritization
⏱️ 5 min

The mapping contract is entity-specific — designed for Assets. background-data-tables.xlsx lists ~11 BEM entities (employees, departments, PM procedures, spare parts, service catalogue) that follow the same import pattern with different field targets.

- Assets is the pilot entity — contract pattern proven via CAFM walkthrough
- Same two-layer matching (column + value) applies to other entities

**To resolve:** Which BEM entity after Assets should be the next priority for the import pipeline.

## Meeting Format

- **Type:** Technical alignment
- **Attendees:** Rein, Marius
- **Expectation:** Rein brings knowledge of BEM's import API constraints
- **Outcome:** Resolved Undefined markers in the Step 1+2 design doc — hierarchy, mapping contract, and enum sections

## Related

- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852)
- **Design Doc:** [Step 1+2 Setup Phase](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design)
- **Parent:** [Chain 1B: Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
