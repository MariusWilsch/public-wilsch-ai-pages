---
publish: true
---

# Step 1+2: Setup Phase — Hierarchy Understanding & Column Mapping
[[client-archibus]]

Design of the interactive Setup Phase for the AI Bulk Data Entry pipeline. Step 1 maps the client's data columns to BEM hierarchy levels. Step 2 maps the client's data columns to BEM schema fields. Together they produce the mapping contract that Step 3 consumes.

---

## Problem Statement

**Client data arrives in the client's language, not in BEM's language.**

Every client structures their facility data differently — different column names, different hierarchy depth, different conventions. Before Step 3 can process data level-by-level, two translations must happen: which columns represent hierarchy levels (Step 1), and which columns map to which schema fields (Step 2).

Today, the implementer performs both translations manually by filling out BEM's import Excel template. The AI takes over this translation; the implementer only confirms or corrects.

**Preconditions:**
- Client has provided a data file (Excel/CSV) with their facility data
- BEM asset schema is known and stable (35 fields, 9 hierarchy levels)
- Step 3 (fill level-by-level) is operational and waiting for the mapping contract

**Value proposition:** The implementer no longer fills out the import Excel template manually. The AI reads their data and produces the mapping — the implementer's time shifts from data entry to client communication.

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | AI produces a confirmed mapping contract from any client Excel — no manual template filling required |
| **Success** | Implementer uploads a data file, reviews AI's proposed mappings in conversation, and confirms with minimal corrections |
| **Done test** | Can Step 3 consume the mapping contract and process data without asking "which column is this?" → If YES → setup is complete |

---

## Approach

### Part 1: Hierarchy Inference (Step 1)

The implementer uploads a raw client Excel. The AI reads it and proposes which client columns map to which of the 9 BEM hierarchy levels.

**Inference mechanism:** Semantic analysis — not column name matching alone. The AI combines three signals:
1. **Column names** — semantic similarity to hierarchy level names
2. **Value content** — what the actual data values suggest (e.g., "CONCOURSE C" suggests a building-scale structure)
3. **Value cardinality** — few unique values = higher level, many = lower level

Column names alone are insufficient. Client data uses domain-specific naming (e.g., "AREA", "SUB AREA OF FACILITY", "LOCATION" in airport operations) that requires semantic reasoning to map to BEM levels like Building, Floor, Room.

**Authority model:** AI proposes with its best understanding, probes with useful questions when uncertain, but the implementer owns the lock-in decision. This is a conversational interaction — the AI presents, the implementer reacts, and the loop continues until the implementer explicitly confirms.

**Scope boundary:** AI maps only what exists in the client's data. It does not suggest adding hierarchy levels that the data doesn't contain. The value proposition is translating client data into BEM's language, not restructuring the client's data model.

**Rejection flow:** If the implementer disagrees, they provide context ("AREA means campus zones, not buildings") and the AI re-proposes. The correction loop is conversational — no re-upload, no UI reassignment. Loop runs until explicit confirmation.

**Output:** Confirmed mapping of client columns → BEM hierarchy levels. Step 3 uses this mapping to generate phantom location assets (Building, Floor, Room records that don't exist in the client's Excel but are required by BEM's parent-child structure).

#### Proposal Artifact

The AI presents its hierarchy proposal using progressive disclosure:

1. **Level names** (must-have) — which client columns map to which BEM levels
2. **Sample values + lightweight reasoning** (should-have) — evidence from the data, one line per level
3. **Expanded reasoning** (on-demand) — implementer can ask the AI to explain its inference

All levels appear in a single proposal. Uncertain levels are flagged inline — the AI does not split confident and uncertain levels into separate phases.

**Example — clean data (CAFM sample, 200 rows):**

| BEM Level | ← Client Column | Sample Values | Confidence |
|-----------|-----------------|---------------|------------|
| Building | Location | Building A, B, C, D | High |
| Floor | Floor | 1, 2, 3, G | High |
| Room | Room No. | 315, 330, 173 | High |

**Example — domain-specific data (Housekeeping Tracker, FMM Qatar):**

| BEM Level | ← Client Column | Sample Values | Confidence |
|-----------|-----------------|---------------|------------|
| Complex | AREA | CONCOURSE A, B, C | ⚠️ Medium |
| ??? | SUB AREA OF FACILITY | PTC, RETAILS | ⚠️ Uncertain |
| Room | LOCATION | DEPARTURE GATE C4 | High |

The clean data resolves in one confirmation. The domain-specific data triggers a correction loop — the AI surfaces the ambiguity with its reasoning, and the implementer provides the external context the data alone cannot supply.

#### Hierarchy Type Descriptions

BEM defines 9 hierarchy levels (Campus → Site → Complex → Property → Building → Floor → Stairwell → Suite → Room). The AI infers which levels apply by matching the client's data against **descriptions and examples per hierarchy AssetType** — not just the type name.

Without descriptions, the AI is guessing from a single word ("Complex" vs "Building"). With descriptions, the AI matches "4 concourse zones in a terminal" against a defined pattern.

**Proposed descriptions (starting point — to be validated with Rein):**

| BEM Type | Proposed Description | Examples |
|----------|---------------------|----------|
| Campus | Entire grounds spanning multiple buildings and sites | University campus, hospital campus |
| Site | A distinct land area within a campus | North wing, Terminal 1 |
| Complex | A group of related zones sharing infrastructure | Airport concourse, shopping mall wing |
| Property | A land parcel or named estate with its own address | Main Office Park |
| Building | A single enclosed structure | Office Tower A, Warehouse B |
| Floor | A horizontal level within a building | Floor 1, Ground Floor, Mezzanine |
| Stairwell | A vertical access shaft (sibling of Floor) | Stairwell A, Fire escape north |
| Suite | A named functional unit within a floor | Suite 204, Lab B, Retail zone |
| Room | An individual enclosed space | Room 315, Server Room, Gate C4 |

**Undefined:** Hierarchy type descriptions — BEM's asset_types file contains only structural codes (hierarchy depth), no descriptions or examples. Proposed descriptions above are AI-generated starting points. See [Meeting Agenda: Step 1+2 Hierarchy & Interpretation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-hierarchy-interpretation).

#### Name Enrichment

After hierarchy confirmation (Step 1, Beat 2), the AI proposes a naming pattern as a separate confirmation. BEM stores all asset types in a single table — a floor named "3" is unreadable without its parent context. The AI compiles readable names by aggregating hierarchy context: "Building D Floor 3 Room 315" instead of just "315".

The enrichment rule is contextual: if the client's data already contains readable names (e.g., "CONCOURSE C"), the AI passes them through. If the data contains only codes or numbers (e.g., "3"), the AI enriches. The AI discriminates when to apply the rule — this is interpretation, not copying. The discrimination rule runs behind the scenes; the AI presents the result for confirmation.

Readability validation is an AI concern — the AI flags unreadable names (e.g., "X1", "ABC123") during the interactive setup phase. The API accepts anything; the quality gate is in the AI layer.

**Interaction placement:** Name enrichment is presented as a separate confirmation after the hierarchy is locked in (Beat 2 of Step 1). This keeps the hierarchy decision and the naming decision as separate cognitive loads — the implementer focuses on one thing at a time.

**Example — CAFM sample (clean data):**

> **AI (Beat 2):** Some of your hierarchy values are just numbers. I'd like to compile readable names:
>
> | Level | Raw Value | → Enriched Name |
> |-------|-----------|----------------|
> | Building | Building A | Building A *(already readable — pass through)* |
> | Floor | 3 | Building A Floor 3 *(enriched)* |
> | Room | 315 | Building A Floor 3 Room 315 *(enriched)* |
>
> Buildings keep their original names. Floors and rooms get parent context added. Agree?

**Example — Housekeeping Tracker (domain-specific data):**

> **AI (Beat 2):** Your hierarchy values are already readable names — no enrichment needed:
>
> | Level | Raw Value | → BEM Name |
> |-------|-----------|------------|
> | Complex | CONCOURSE C | CONCOURSE C *(pass through)* |
> | Room | DEPARTURE GATE C4 | DEPARTURE GATE C4 *(pass through)* |
>
> All names are already descriptive. I'll use them as-is.

**Undefined:** The exact naming enrichment logic and rule discrimination boundaries need empirical testing and stakeholder alignment. See [Meeting Agenda: Step 1+2 Hierarchy & Interpretation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-hierarchy-interpretation).

---

### Part 2: Column-to-Schema Mapping (Step 2)

With the hierarchy established, the AI maps remaining client columns to BEM's 35 schema fields.

**Mapping model:** Two-layer. First, column-level matching: every client column is either **mapped** (AI is confident of the BEM field) or **flagged** (AI cannot determine the match). Second, for enum fields only, value-level matching: each client value is either a direct match or needs translation.

Mapped columns go through batch confirmation — the implementer sees all confident mappings in one table and confirms. Flagged columns get individual 1x1 resolution.

```
┌─────────────────────────────────────────────────────┐
│  Layer 1: Column-Level Matching                     │
│                                                     │
│  Client Column        →  BEM Field                  │
│  ─────────────           ─────────                  │
│  "Status"             →  status        ✅ Mapped    │
│  "Condition"          →  ???           ⚠️ Flagged   │
│  "Serial No."         →  serialNumber  ✅ Mapped    │
└──────────────────────────┬──────────────────────────┘
                           │
                    (enum fields only)
                           │
                           ▼
┌─────────────────────────────────────────────────────┐
│  Layer 2: Value-Level Matching                      │
│                                                     │
│  Client Value    →  BEM Enum Value                  │
│  ────────────       ──────────────                  │
│  "Active"        →  "Active"           ✅ Match     │
│  "Inactive"      →  ???                ⚠️ Suggest   │
│                     ├─ "Out of Service"              │
│                     ├─ "Not Ready"                   │
│                     └─ "In Storage"                  │
└─────────────────────────────────────────────────────┘
```

**Confirmation surface:** Chat-rendered table as the primary review artifact. The implementer sees the proposed mappings and confirms in conversation. Excel export is available as an option for deeper review.

**Training data:** The more diverse client Excels the team provides internally, the better the AI's context engineering becomes for column name recognition. Self-improving agent loops across deployments are a post-deployment architecture concern.

#### Column Taxonomy

Every client column falls into one of six categories. The category determines how the mapping contract records it and how Step 2 presents it to the implementer.

| Category | Contract Records | Step 2 Behavior |
|----------|-----------------|-----------------|
| **Hierarchy assignment** | clientColumn, bemLevel | Handled in Step 1 (Part 1) |
| **Direct passthrough** | clientColumn → bemField | Batch confirm — AI is confident |
| **Passthrough with fallback** | clientColumn → bemField + auto-fallback | Auto-resolved — no implementer flag |
| **Enum mapping** | clientColumn → bemField + value translation table | Batch confirm (column) + 1x1 (unmatched values) |
| **Flagged unmappable** | flagged with options: overflow / exclude | 1x1 — AI flags neutrally, implementer decides |
| **Excluded (Skip tier)** | excluded, reason recorded | Excluded — no implementer decision |

**CAFM example (15 columns → 6 categories):**
- Hierarchy: Location, Floor, Room No. (3 columns → Part 1)
- Direct passthrough: Serial No.→serialNumber, Model→modelSpecific, BrandSpecific→brandSpecific, 6 dates, 3 contacts (12 columns)
- Passthrough with fallback: Asset ID→id (row index if no client ID column)
- Enum mapping: Asset Type→assetType, Status→status
- Flagged unmappable: Condition, Maintenance Frequency (BEM's full table has `condition` and `classification_for_maintenance` but the import API doesn't expose them yet)
- Excluded: Assigned To (assignedPortfolioEmployee is Skip tier — requires Guid lookup)

#### Enum Resolution

For enum fields (AssetType, Status, Country), column-level matching is insufficient — the client's values must also match BEM's predefined enum values. The AI resolves enums during Step 2's interactive phase:

1. AI reads the client's unique values for the column
2. AI compares against BEM's enum reference (asset_types, asset-status-enum, countries table)
3. Direct matches pass through; unmatched values get ranked suggestions from the enum
4. Implementer confirms or picks from suggestions for each unmatched value
5. The confirmed translation table enters the mapping contract

The contract's enum map must be complete — every client value that appears in the data has a confirmed BEM translation. No unresolved values pass through to Step 3. A default fallback exists per enum (e.g., "Equipment" for unknown AssetType values) to catch values that appear in future data rows Step 2 never encountered.

**Undefined:** Handling of unmappable columns — AI flags neutrally with options (overflow to statusDetail, exclude), implementer decides. BEM's full asset table has `condition` (nvarchar 50) and `classification_for_maintenance` (nvarchar 128) that could solve common unmappable cases if added to the import API. See [Meeting Agenda: Mapping Contract & Enum Resolution](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-hierarchy-interpretation).

**Undefined:** Specific enum translations (e.g., "Inactive" → which BEM Status value?) need Rein input on intended semantic mapping. The pattern is defined; specific mappings require domain knowledge. See [Meeting Agenda: Mapping Contract & Enum Resolution](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-hierarchy-interpretation).

---

### Part 3: Mapping Contract (Step 1+2 → Step 3 Handoff)

The combined output of Step 1 and Step 2 is the **mapping contract** — the artifact that Step 3 consumes to process data level-by-level.

**Role:** The mapping contract is the single source of truth for Step 3. Everything Step 3 needs to know about the client's data structure — which columns are hierarchy levels, which columns map to which BEM fields, which columns are excluded — lives in this contract. Step 3 never interprets client data directly; it reads through the contract.

**Visibility:** Internal JSON, invisible to the implementer. The AI internalizes BEM's schema and produces structured output from the raw client Excel. The implementer validates the mapping through the chat-rendered table (Part 2), not by inspecting the contract itself.

**Design principle — absence = omission:** Fields not present in the contract are omitted from Step 3's JSON output. The BEM API treats missing fields as null. No explicit "skip" entries needed for unmapped BEM fields — their absence IS the signal.

**Field placement:** Address fields (address, city, state, postalCode, country) are placed on the highest container node in the hierarchy. All other mapped fields are placed on equipment leaf nodes. This follows the principle that location-level data lives on the container, equipment-level data lives on the leaf.

#### Contract Elements

The contract contains three elements, each corresponding to a Step 1 or Step 2 output:

**Element 1 — Hierarchy Assignments** (Step 1 output):
An ordered array where position defines nesting depth. Each entry maps a client column name to a BEM hierarchy level. Name enrichment happens during Step 1 before the contract is finalized — the contract carries the mapping, not the enrichment logic.

```json
"hierarchy": [
  {"clientColumn": "Location",  "bemLevel": "Building"},
  {"clientColumn": "Floor",     "bemLevel": "Floor"},
  {"clientColumn": "Room No.",  "bemLevel": "Room"}
]
```

**Element 2 — Field Mappings** (Step 2 output):
A flat lookup — each entry maps a client column to a BEM field. The AI knows field types and lengths from its static context (AssetImportDescription); the contract carries only the mapping, not the schema. The `id` field uses an explicit fallback when the client has no Asset ID column.

```json
"fieldMappings": [
  {"clientColumn": "Asset ID",      "bemField": "id"},
  {"clientColumn": "Asset Name",    "bemField": "name"},
  {"clientColumn": "Serial No.",    "bemField": "serialNumber"},
  {"clientColumn": "Model",         "bemField": "modelSpecific"},
  {"clientColumn": "Manufacturer",  "bemField": "brandSpecific"},
  {"clientColumn": "Purchase Date", "bemField": "datePurchased"},
  {"clientColumn": "Warranty Expiry","bemField": "warrantyTo"}
]
```

When the client has no Asset ID column, the fallback is explicit:

```json
{"clientColumn": null, "bemField": "id", "fallback": "rowIndex"}
```

**Element 3 — Enum Rules** (Step 2 output):
Per-enum-field translation tables confirmed during Step 2. Every client value that appears in the data must have a confirmed BEM translation — no nulls or unresolved values. The `default` catches values that appear in future data rows Step 2 never encountered.

```json
"enumRules": {
  "assetType": {
    "map": {"Safety": "Safety Equipment", "HVAC": "HVAC equipment", "Electrical": "Other EPE"},
    "default": "Equipment"
  },
  "status": {
    "map": {"Active": "Active", "Inactive": "Out of Service", "New": "Not Ready"},
    "default": "Unknown"
  }
}
```

**Step 3 consumption:** Step 3 walks the hierarchy array top-to-bottom, building the tree. For each equipment row, it reads field mappings to populate BEM fields and applies enum rules for translation. Absent fields are omitted from the JSON. Step 3 performs no interpretation — it executes the contract.

#### Contract Design Principles

**Transformation spec, not mutation.** The contract is a set of transformation rules applied over the immutable source Excel. The original data is never modified. With all three artifacts preserved (source Excel + contract + output JSON), every transformation is traceable and diffable.

**AI/CLI boundary.** The contract separates the intelligent layer (Steps 1+2: semantic inference, interactive confirmation) from the programmatic layer (Step 3: tree building, API calls). The AI produces the contract; the CLI/MCP consumes it mechanically.

**Backpressure updates the contract.** When the BEM API rejects an enum value, the AI corrects the contract's enum rules — not just the individual JSON node. This fixes the root cause: all rows with that value get the corrected translation, including buildings not yet processed. Data-specific errors (e.g., name too long) are fixed per-node.

#### Data Type Detection (Step 0)

Before column mapping begins, the AI assesses whether the uploaded data is an asset inventory. Not all client files contain equipment data — operational trackers (work orders, maintenance logs, staff lists) contain location hierarchies but no asset properties. The AI surfaces this upfront: "This appears to be a work order tracker, not an asset inventory. Location hierarchy can be extracted, but there are no equipment records."

The mapping contract is entity-specific — designed for the Assets import pipeline. Other BEM entities (employees, departments, PM procedures) would follow the same pattern with entity-specific field targets.


---

### Part 4: Interaction Flow

The implementer's journey through Steps 0-2 as a chat-based conversation inside BruceBEM's AI interface. The implementer uploads a file, the AI analyzes it, and the conversation walks through each step until the mapping contract is confirmed and Step 3 can begin.

#### Context Layers

The AI draws from three context layers during the setup phase:

| Layer | What | When Loaded |
|-------|------|-------------|
| **Static** | Four BEM reference artifacts baked into the AI agent: (1) [AssetImportDescription](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) — 37 import fields with data types, lengths, descriptions; (2) [asset_types.xlsx](https://docs.google.com/spreadsheets/d/1Wc1BL18e5Vaxx7bAzsvxAeWojfvxRrWd/edit) — AssetType enum; (3) [asset-status-enum.csv](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset-status-enum.csv) — Status enum; (4) Countries table (249 entries). Google Sheets are working copies — authoritative versions live on Bruce Partners SharePoint. | Pre-loaded before any client interaction |
| **Dynamic** | The mapping contract — hierarchy assignments, field mappings, enum rules. Built progressively through Steps 1+2 confirmations | Accumulated during the interactive phase |
| **Runtime** | API backpressure — validation errors, enum lists returned on rejection, correction signals | Generated during Step 3 autonomous processing |

The static layer is the AI's prior knowledge. The dynamic layer is the output of the interaction flow described below. The runtime layer is Step 3's feedback mechanism (see [Step 3 Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)). The exact encoding of each layer is an implementation detail — the design-level decision is the three-layer separation and progressive accumulation.

#### Design Principles

Three principles govern the interaction flow across all steps:

**1. Fast lane / slow lane.** AI presents confident work as quick-scan batch tables, uncertain work as individual 1x1 engagement. This pattern applies universally — hierarchy, column mapping, enum resolution. The implementer's attention goes where it's needed, not spread across everything equally.

**2. Announce and ask for foundational decisions, announce and advance for low-stakes.** Data type detection (Step 0) is low-stakes for supported types — the AI announces and advances. Hierarchy confirmation (Step 1) is foundational — it determines the entire tree structure. The AI always asks. The weight of the confirmation gate matches the weight of the decision.

**3. Progressive trust.** First import = full explicit gates at every step. As the implementer and AI build mutual understanding, gates can condense. The AI sets expectations upfront so the implementer knows the journey. v1 uses **roadmap-first** as the opening mode: "I'll analyze your file in 3 steps..." before showing results. Two alternative modes exist for future empirical testing: **show-first** (jump to first result, no preamble) and **result-first** (entire analysis at once, closest to Miguel's "boom" demo feedback).

See [Anthropic Skill Best Practices — Progressive Disclosure](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) for the underlying pattern: AI recommends one option with an escape hatch, not a menu of choices.

#### Happy Case Walkthrough (CAFM Sample)

The CAFM asset upload sample (201 rows, 16 columns, 4 buildings) represents the clean-data path. 8 messages, 4 confirmation gates.

**Message 1** — Implementer uploads `cafm-asset-upload-sample.xlsx`.

**Message 2 — Step 0 + Step 1, Beat 1 (data type + hierarchy):**

> `cafm-asset-upload-sample.xlsx` — 201 rows, 16 columns. Equipment asset data.
>
> **Hierarchy detected:**
>
> | BEM Level | ← Your Column | Sample Values |
> |-----------|---------------|---------------|
> | Building | Location | Building A, B, C, D |
> | Floor | Floor | 1, 2, 3, G |
> | Room | Room No. | 116, 232, 333 |
>
> 3 levels. Does this match your data structure?

Data type announced without a gate (high confidence, supported type). Hierarchy presented with an explicit question — the implementer must confirm before advancing.

**Message 3 — Step 1, Beat 2 (name enrichment):**

After hierarchy confirmation:

> Some of your hierarchy values are just numbers. I'd like to compile readable names:
>
> | Level | Raw Value | → Enriched Name |
> |-------|-----------|----------------|
> | Building | Building A | Building A *(already readable)* |
> | Floor | 3 | Building A Floor 3 |
> | Room | 315 | Building A Floor 3 Room 315 |
>
> Buildings keep their original names. Floors and rooms get parent context added. Agree?

Separate cognitive load from hierarchy. The discrimination rule ran behind the scenes — the AI shows the result.

**Message 4 — Step 2 (column mapping batch):**

> **Column mapping — 13 remaining columns analyzed:**
>
> **Mapped (9 confident):**
>
> | Your Column | → BEM Field |
> |-------------|------------|
> | Asset Name | assetName |
> | Serial No. | serialId |
> | Manufacturer | manufacturer |
> | Model | model |
> | Purchase Date | datePurchased |
> | Warranty Expiry | warrantyExpiry |
> | Condition | condition |
> | Status | status |
> | Assigned To | assignedPortfolioEmployee |
>
> **Already used in hierarchy:** Location, Floor, Room No.
>
> **Needs your input (2):**
> 1. `Asset Type` → assetType — one enum value didn't match
> 2. `Maintenance Frequency` — no BEM field found
>
> Do the 9 mappings above look correct? If so, we'll resolve the 2 flagged items next.

Confident mappings in a batch table — still a confirmation gate. The implementer reviews the batch and may disagree with a mapping before moving on. Flagged items listed with count so the implementer knows what's coming. Hierarchy columns called back to Step 1. Internal IDs (Asset ID, ParentId) silently handled.

**Message 5 — Step 2 (enum resolution, 1x1):**

For each enum mismatch, the AI leads with its top recommendation plus alternatives:

> `Asset Type` → BEM's `assetType`. 3 of 4 values matched:
>
> | Your Value | → BEM AssetType | Match |
> |------------|----------------|-------|
> | Safety | Safety Equipment | Fuzzy |
> | IT Equipment | IT Equipment | Exact |
> | HVAC | HVAC equipment | Fuzzy |
> | Vertical Trans. | **?** | No match |
>
> For "Vertical Trans." I'd suggest **Vertical Transportation**. Other options: Other Equipment, Mechanical Equipment. [See all BEM types]
>
> Which one fits?

Then for Status:

> `Status` → BEM's `status`. 1 of 3 matched:
>
> | Your Value | → BEM Status |
> |------------|-------------|
> | Active | Active *(exact)* |
> | Inactive | **?** — I'd suggest **Out of Service** ("asset no longer used, could be out of service for a while") |
> | New | **?** — I'd suggest **Not Ready** ("asset being installed or commissioned") |
>
> Does "Inactive" mean the same as "Out of Service" in your client's context?

One mismatch at a time. AI provides BEM descriptions to help the implementer decide. Progressive disclosure: top recommendations shown, full enum list one click away.

**Message 6 — Step 2 (unmappable column):**

> `Maintenance Frequency` doesn't match any of BEM's 35 import fields.
>
> **→ Exclude** — this data won't be imported.
>
> If you'd rather keep it, I can store it in `statusDetail` (free-text field). What would you prefer?

Default recommendation with escape hatch.

**Message 7 — Summary + handoff:**

> **Mapping complete.** Summary:
>
> - **Hierarchy:** Building → Floor → Room (names enriched)
> - **Mapped:** 9 equipment fields
> - **Enums resolved:** AssetType (4 values), Status (3 values)
> - **Excluded:** Maintenance Frequency
>
> Ready to process 201 rows across 4 buildings. Proceed?

Last gate before autonomous processing. In-chat summary is the default review artifact.

The summary above is the default handoff artifact. A detailed "decision receipt" is available on demand — a chat-native expandable view showing every mapping row, every enum translation, and every exclusion with reason. Two faces, one artifact: the summary (quick scan) and the receipt (full audit). The receipt is in-chat, not Excel — it aggregates heterogeneous decision types (hierarchy, mappings, enums, exclusions) that don't fit a single spreadsheet format. Test empirically whether implementers use the receipt or just confirm the summary.

**Message 8 — Step 3 (autonomous processing):**

> Processing...
>
> **Building A:** 3 locations + 52 equipment ✓
>
> **Building B:** 3 locations + 48 equipment ✓
>
> **Building C:** 3 locations + 55 equipment ✓
>
> **Building D:** 3 locations + 46 equipment ✓
>
> **Done.** 201 equipment + 12 location assets inserted. 0 errors.
>
> [Export to Excel] [View in BruceBEM]

In-chat progress by building. Excel export available on demand for detailed review.

#### Step 0 Scenarios

| Scenario | AI Behavior | Gate? |
|----------|------------|-------|
| **0a: Recognized equipment data** | Announces "equipment asset data" and advances to hierarchy | No gate — announce and advance |
| **0b: Recognized but unsupported** (e.g., work orders) | "This looks like work order data. I can only process asset/equipment data right now." | Stop — blocked |
| **0c: Ambiguous data type** | "I'm not sure if this is equipment data or work orders. What type of data is this?" | Ask — implementer decides |

**Undefined:** The exact mechanism for data type detection (which signals the AI uses to distinguish asset data from work orders) needs Rein input. See [Meeting Agenda: Step 1+2 Hierarchy & Interpretation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-hierarchy-interpretation).

#### Step 1 Scenarios

| Scenario | Description | Example |
|----------|------------|---------|
| **1a: Clean, all high confidence** | All columns map clearly to hierarchy levels | CAFM sample: Location→Building, Floor→Floor, Room No.→Room |
| **1b: Partial confidence** | Some levels confident, some uncertain | Housekeeping Tracker: AREA→Complex (medium), SUB AREA→??? (uncertain), LOCATION→Room (high) |
| **1c: No hierarchy detected** | Flat equipment list, no location columns | Inventory export with only equipment attributes (name, serial, type) |
| **1d: Too many candidate columns** | Multiple columns could be hierarchy levels | Facility export with AREA, ZONE, SUB AREA, LOCATION, SECTOR — implementer disambiguates |
| **1e: Hierarchy found, wrong data type** | Location columns exist but non-hierarchy columns suggest work orders, not equipment | Housekeeping Tracker: has AREA/LOCATION hierarchy but columns are Task Description, Completion Status, Scheduled Date |

Scenario 1e is Step 0 and Step 1 interacting — data type detection gets refined BY the hierarchy analysis.

#### Step 2 Scenarios

| Scenario | Description | Example |
|----------|------------|---------|
| **2a: Mostly confident** | Most columns match, few flagged | CAFM: 9 mapped, 2 flagged |
| **2b: Many fuzzy matches** | Cryptic column names, AI guesses | Abbreviated names: SN→serialId?, MFR→manufacturer?, EQ_NM→assetName? |
| **2c: All confident** | Clean template-based data, every column matches | Rare — all 12 columns match, no enum conflicts, no flags |

#### Future Extension: Existing-Database Scenario

When the BEM database already contains assets (buildings, floors, rooms), the interaction flow gains a fork: "Are these buildings new or do they already exist?" This scenario introduces a pre-check against BEM's existing data using the `get asset by name` endpoint. Documented here as a known future extension — not designed in the current flow.

---

## Source

- **Design docs:**
  - [Chain 1B: Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design) (parent router)
  - [Step 3: Fill Level-by-Level](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
- **Transcripts:**
  - [AI Data Entry Demo (Feb 23, 2026)](/Users/verdant/Downloads/AI Data Entry Demo Transcript.txt)
- **Data artifacts:**
  - [AssetImportDescription (36 fields)](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit)
  - [Housekeeping Tracker (FMM)](/Users/verdant/Downloads/Housekeeping Tracker Jan 2026 v1.xlsb) — second Excel sample for hierarchy inference testing
- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852)
- **Project Index:** [#373](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/transcript-index-373)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/7d4d0137-c41c-4df3-835f-0218a90eba19.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/919879a0-f08e-4ce4-acfd-8ef68c39ef55.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch__archibus-bulk-import/23cb9666-8d53-4479-ab6c-e775b7020083.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch__archibus-bulk-import/58bfde61-b97e-45fe-9a57-c8bdc231b7fe.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/57d480dc-e7ea-4ee3-b96f-dce4a2e6f4ca.jsonl
- **Reference:** [Anthropic Skill Best Practices — Progressive Disclosure](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
