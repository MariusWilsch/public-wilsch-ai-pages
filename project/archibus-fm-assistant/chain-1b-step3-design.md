---
publish: true
---

# Chain 1B: Step 3 Fill Level-by-Level - Design Document
[[client-archibus]]

Transform a mapped equipment table into nested JSON that Bruce BEM's insertion API can accept, processing one top-level hierarchy element at a time with self-correction on errors.

---

## Problem Statement

**The gap between client data and system schema is the implementer's burden.**

Populating Bruce BEM's background data requires understanding the client's hierarchy, mapping their columns to schema fields, and filling data level-by-level. Step 3 handles the final phase: given a mapped schema (Step 2 output) and a confirmed hierarchy (Step 1 output), transform the client's equipment table into nested JSON that Bruce BEM's insertion API can accept directly.

**Preconditions:**
- Step 1 complete: hierarchy detected and confirmed by implementer (e.g., Building → Floor → Room)
- Step 2 complete: column mapping from client schema to Bruce BEM ImportTemplate fields established
- Input is an equipment table with hierarchy embedded in columns (not location rows)
- Location-based assets (buildings, floors, rooms) must be created from column values — they don't exist as rows in the input

---

## Success Definition

- **Goal:** Equipment table transformed into AI-generated hierarchical JSON that Bruce BEM can insert, with self-correction on validation errors

- **Success:** For each top-level hierarchy element (e.g., 4 buildings = 4 JSONs): generate nested JSON → validate via API → if error, AI corrects and resubmits → insert when clean → move to next element. All elements populated.

- **Done test:** "Can I produce a valid JSON for one building that the API accepts without errors?" → If YES for all buildings → Step 3 is complete

---

## Approach

### 1. Filter by Top-Level Element

For each unique value at the highest hierarchy level, process one element and all its children at a time. This scopes the work: instead of transforming the entire table at once, AI handles one building at a time. Processing one building keeps individual JSON payloads small for faster API validation and avoids transferring the entire dataset in a single request.

```
Equipment table (N buildings)
    │
    ├─→ Run 1: Building A ← process this first
    ├─→ Run 2: Building B
    ├─→ Run 3: Building C
    └─→ Run N: ...
```

**Example — Building A selected (3 of 18 equipment rows, all columns):**

| Asset | Name | Type | Floor | Room | Manufacturer | Model | Serial No. | Purchase Date | Condition | Status | Assigned To |
|-------|------|------|-------|------|-------------|-------|------------|---------------|-----------|--------|-------------|
| A008 | Safety-008 | Safety | All | 116 | LG | FX100 | SN000008 | 2018-05-04 | New | Inactive | Team B |
| A010 | Vertical Trans.-010 | IT Equipment | 3 | 333 | LG | LEDX200 | SN000010 | 2020-03-14 | Poor | Active | Team C |
| A034 | Electrical-034 | Electrical | 1 | 194 | Honeywell | EliteBook | SN000034 | 2019-02-09 | Fair | Active | Team F |

**Reference:** [cafm-asset-upload-sample.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/cafm-asset-upload-sample.xlsx) — the raw client input before any transformation.

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW), [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ)*

### 2. Extract Hierarchy Chain

Each equipment row contains its full parent chain embedded in columns. Read the hierarchy columns left-to-right to extract the location path.

**Assumption:** Step 1 has already confirmed the hierarchy structure (e.g., Building → Floor → Room). Step 2 has established column mappings AND surfaced awareness flags for anomalous values (e.g., "All", "None", "every"). Step 3 receives this awareness and acts — it transforms, corrects, and backfills without re-investigating. The hierarchy is embedded in the row — we do not infer or create it.

**Example — chains from the 3 Building A rows above:**

```
A008: Building A → Floor All → Room 116
A010: Building A → Floor 3   → Room 333
A034: Building A → Floor 1   → Room 194
```

The chain determines where each equipment asset will attach in the hierarchy. All hierarchy values are treated as valid location-based asset names — "All", "G", numbered floors are all processed identically. No values are skipped or special-cased.

**Reference:** Same source file — each row's Location, Floor, and Room columns encode the chain.

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW)*

### 3. Deduplicate Per Level

Extract unique values at each hierarchy level. Multiple equipment rows sharing the same location = one location asset to create. 18 equipment rows might reference Floor 3 five times — but Floor 3 is one location asset, not five. Deduplication extracts the distinct set of parents that need to be created.

**Example — Building A (18 equipment rows):**

| Level | Unique Values | Count |
|-------|---------------|-------|
| Building | Building A | 1 |
| Floor | 1, 2, 3, 4, All | 5 |
| Room (Floor 1) | 194, 494 | 2 |
| Room (Floor 2) | 286, 321, 395, 436 | 4 |
| Room (Floor 3) | 277, 333, 413, 431, 476 | 5 |
| Room (Floor 4) | 235 | 1 |
| Room (Floor All) | 116, 175, 232, 377, 379, 473 | 6 |
| **Total location assets to create** | | **24** |

**Identity rule:** Room identity = parent floor + room number. Room 333 under Floor 3 is a different asset than a hypothetical Room 333 under Floor 2. This rule is structurally necessary — the nested JSON tree requires each room to be a child of exactly one floor. Sample data confirms: Room 431 exists on both Floor 3 and Floor "All" in Building A.

**Reference:** [Processed Excel Table.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/Processed%20Excel%20Table.xlsx) — the data after Step 2 column mapping.

*Source: [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ)*

### 4. Create Location-Based Assets

The source data contains only equipment rows. Location assets (buildings, floors, rooms) are implicit in the hierarchy columns and must be created as new rows with the **same schema** — they're all entries in the same assets table, differentiated by AssetType.

**What AI does:** Extract unique locations from Element 3's dedup output → create new asset rows for each.

**Example — Processed table for Building A (from [Processed Excel Table](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/Processed%20Excel%20Table.xlsx)):**

| Asset Code | Asset Name | Asset Type | Location |
|------------|------------|------------|----------|
| *A060* | *IT Equipment-060* | *HVAC* | *Building A* |
| *A065* | *Vertical Trans.-065* | *Plumbing* | *Building A* |
| *...* | *(34 equipment rows)* | | |
| **A201** | **Building A** | **Building** | **Exeter rd 25** |
| **A203** | **All** | **Floor** | **Building A** |
| **A204** | **1** | **Floor** | **Building A** |
| **A207** | **G** | **Floor** | **Building A** |
| **A208** | **Room 201** | **Room** | **Building A** |
| **A215** | **Room 194** | **Room** | **Building A** |

*Italic = original equipment rows. **Bold = AI-generated location assets.***

The hierarchy is expressed in the nested JSON structure (Element 5), not via explicit Parent Codes.

**Attachment rule:** Equipment attaches to the lowest available hierarchy level. The recursive fallback applies unconditionally — regardless of floor value ("All", "G", empty, or numbered). If room is missing, attach to floor. If floor is missing, attach to building. No building = data error. This rule has no exceptions.

**Floor "All":** Created as-is — it is a real location asset, not a skip signal. Rooms nest under it normally. Equipment with Floor = "All" and a room attaches to the room (which is a child of Floor "All"). In sample data, all 34 Floor "All" rows have a room number.

**Reference:** [asset_types 1.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset_types%201.xlsx) — predefined AssetType enum (includes Building, Floor, Room).

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW) (Rein: "equipment parent is the room... when the room is empty, it goes to the next level"), [Feb 11 — Rein meeting](https://app.fireflies.ai/view/01KH67KMEA533C0VVJWH7FE63D) (Rein: "let's create this floor", "always climb up until the field level")*

### 5. Build Nested JSON

Transform the flat table (equipment rows + generated location assets from Element 4) into nested JSON that the insertion API can accept. Every asset follows the same schema — location assets and equipment are all rows in the same `assets` table, differentiated by AssetType.

#### Field Tiers

| Tier | Fields | Rule |
|------|--------|------|
| **Required (AI-filled)** | Name, AssetType | Must be in JSON. Insert fails without them. |
| **Filled by Bruce** | Id, ParentId, OwnerId, OspId | Leave null — system auto-assigns. |
| **Skip (AI ignores)** | AssignedPortfolioEmployee | Too complex for PoC — requires employee list lookup. |
| **Optional (AI-filled)** | All other fields | Include only if the source data provides them. AI maps, doesn't fabricate. |

**Key field mappings (from Feb 11 meeting):**

| Client Column | JSON Field | Rule |
|--------------|------------|------|
| Asset ID | OtherCode | Copy for traceability and future integrations |
| Manufacturer | StatusDetail | No manufacturer field in schema — overflow to StatusDetail (pipe-separated with other unmappable fields). Routing unmappable columns to StatusDetail is a Step 2 decision that Step 3 executes. |
| Model | ModelSpecific | "Model specific is for the model" — Rein |
| Country | Country | Fuzzy-match client input ("USA", "DE") to exact country NAME from enum. code_2 ignored. |

**StatusDetail semantic note:** DB field is `StatusDetail` but UI shows "Asset Detail Description." Not status information — it's a description/overflow field.

**Reference:** [AssetImportDescription.xlsx → "Fields Assets" sheet](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/AssetImportDescription.xlsx) — full 36-field schema with data types, lengths, and requirements. Countries enum table provided by Rein (249 entries — 39 have parenthetical suffixes pending cleanup).

#### The Transform

AI performs four sub-operations on each row:

1. **Column mapping** — which Excel column feeds which JSON field (Step 2 output)
2. **Value transformation** — enum matching, date format conversion, country resolution
3. **Structure transformation** — flat rows → nested tree (children inside parents)
4. **Missing value handling** — omit field from JSON body. API treats missing fields as null via .NET model binding. Rein: "we can just skip these fields and not include into the body."

**Value transformation categories (including but not limited to):**

| Category | Examples | Rule | Reference |
|----------|----------|------|-----------|
| **Enum fields** | AssetType, AssetStatus, Country | Must match predefined values. Element 6 backpressure catches mismatches. | [asset_types](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset_types%201.xlsx), [asset-status-enum](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset-status-enum.csv) |
| **Typed fields** | DateTime, double, Guid, String(len) | Must match format from schema. | [AssetImportDescription → Fields Assets](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/AssetImportDescription.xlsx) |
| **Freeform fields** | Name, Address, LocationDescription | Pass through as-is. | Same schema for max lengths |

**Example — transform + backpressure loop:**

```
Raw Excel Row: A008 | Safety | Building A | All | 116 | LG | FX100
    ↓
    1. Column mapping (Step 2: "Type" → AssetType)
    2. Value transform (Safety → Safety Equipment, matching Bruce enum)
    3. Structure transform (nest under Building A → Floor All → Room 116)
    4. Missing value handling (empty cells → omit from JSON)
    ↓
AI-Generated JSON node
    ↓
Element 6: Validate via API
    ↓
API accepts ✅  OR  API rejects ❌ → error → AI corrects → resubmit
```

**Pending Rein deliverables (blocked on updated AssetImportDescription Excel):**
- Field descriptions + example values per field (for AI context)
- SecurityClearanceStatus → ClearanceInstruction rename
- AddressExplanation vs LocationDescription — one to be removed
- QR code field length extension (50 chars insufficient)
- Country table cleanup (remove parenthetical suffixes — needs Miguel)
- Null/empty field handling test confirmation

*Source: [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ), [Feb 11 — Rein meeting](https://app.fireflies.ai/view/01KH67KMEA533C0VVJWH7FE63D), [AssetImportDescription.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/AssetImportDescription.xlsx)*

### 6. Insert via API + Backpressure

Send the AI-generated JSON for one top-level element to the insertion API. Sequential processing — no parallel in PoC.

**Insertion model (validate-then-insert, confirmed Feb 11):**

```
AI-Generated JSON (one building + all children)
    ↓
API validates entire payload
    ↓
First error found → return error (one at a time)
    ↓
AI reads error → corrects JSON → resubmits
    ↓
Repeat until no errors
    ↓
All valid → API inserts everything
```

Nothing is inserted until the entire payload validates. No partial state. Dry run mode confirmed: validation without transaction commit, error return without DB write. Full JSON resubmitted on each correction (not partial updates). Auto-insert vs manual confirmation after dry run passes: deferred to empirical testing — PoC default is auto-insert if no errors.

**Error response format (agreed Feb 11 — Rein: "I was reviewing and I like this"):**

```json
{
  "error": {
    "field": "AssetType",
    "value": "HVAC",
    "reason": "Invalid value. Must match predefined type.",
    "valid_values": ["HVAC equipment", "Air Cooling", "Air Heating", "..."]
  }
}
```

**Implementation boundary:** Elements 1–5 are implementable now (AI-generated JSON production). Element 6 requires Rein's insertion API, which he committed to developing (Feb 11: "I need to start to develop my endpoint").

**Reference:** API specification TBD — to be provided by Rein.

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW) (decisions: tree-walk, stop at first error, sequential), [Feb 11 — Rein meeting](https://app.fireflies.ai/view/01KH67KMEA533C0VVJWH7FE63D) (dry run, error format, full resubmission confirmed)*

### 7. Repeat

Move to next top-level element. The full cycle (validate → correct → dry run → insert) completes per building before moving on — per-building atomic completion. Continue until all elements processed.

---

## Decisions

| Decision | What | Date | Source |
|----------|------|------|--------|
| **Tree-walk** | Process one top-level element and all children at a time | Feb 3 | Rein meeting |
| **Stop at first error** | API returns one error at a time. AI corrects and resubmits. No insertion until all valid. | Feb 3 | Rein meeting |
| **Sequential first** | No parallel processing in PoC | Feb 3 | Rein meeting |
| **AI creates JSON** | With Bruce BEM field names from Step 2 mapping | Feb 3 | Rein meeting |
| **No interactive editing** | AI proposes/processes hierarchy, doesn't modify Excel | Feb 3 | Rein meeting |
| **Asset codes optional** | Not required by API. Don't generate unless client wants. | Feb 6 | Rein: "for me they are not important" |
| **All IDs null** | API assigns PKs on insert. Confirmed explicitly. | Feb 6 | Rein: "I don't need these IDs at all" |
| **Lowest level attachment** | Equipment attaches to lowest known hierarchy level. Empty = fallback up. | Feb 3 | Rein: "equipment parent is the room... when empty, goes to next level" |
| **Nested JSON format** | Children nested inside parents. No explicit ParentId needed — hierarchy encoded structurally. | Feb 11 | Extraction pass session |
| **Same schema for all levels** | Location assets and equipment are all rows in the same assets table, differentiated by AssetType. | Feb 6 | AssetImportDescription |
| **Elements 1-5 now, 6 after API** | JSON generation is implementable. Insertion requires Rein's API. | Feb 11 | Extraction pass session |
| **Floor All as-is** | Create Floor "All" as a real location asset. No special-casing — standard recursive fallback applies. | Feb 11 | Rein: "let's create this floor... it is easier let it be in this way" |
| **Recursive fallback unconditional** | Climb up hierarchy regardless of floor value. No exceptions. | Feb 11 | Rein: "always climb up until the field level" |
| **Excel = field name source** | AssetImportDescription "Fields Assets" sheet is authoritative for JSON field names. API auto-converts casing. | Feb 11 | Rein: "all the field names are as they are in the Excel" |
| **Country = name match** | Fuzzy-match to country name (column A), not code_2. Exact string required. | Feb 11 | Rein: "the country name should match to this list" |
| **Omit empty fields** | Don't include empty fields in JSON body. API treats missing as null. | Feb 11 | Rein: "we can just skip these fields" (pending test confirmation) |
| **Manufacturer → StatusDetail** | No manufacturer field. Combine into StatusDetail as overflow. | Feb 11 | Rein: "combine this information to the value of the field status detail" |
| **OtherCode = client asset ID** | Copy client's original asset ID for traceability. | Feb 11 | Rein: "copy to the field other code... important for integrations" |
| **Error format agreed** | Proposed error response format accepted by Rein. Extensible. | Feb 11 | Rein: "I was reviewing and I like this" |
| **Dry run confirmed** | Validate without inserting. Only insert after all validations pass. | Feb 11 | Rein: "only when we are completely sure... then we can start the real insert" |

---

## Source

**Artifacts:**
- [AssetImportDescription.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/AssetImportDescription.xlsx) — full schema (Fields Assets sheet), hierarchy options, sample data
- [Hierarchical Body.json](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/Hierarchical%20Body.json) — Rein's example nesting structure
- [asset_types 1.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset_types%201.xlsx) — AssetType enum (47 predefined types)
- [asset-status-enum.csv](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset-status-enum.csv) — AssetStatus enum (25 statuses)
- [cafm-asset-upload-sample.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/cafm-asset-upload-sample.xlsx) — raw client input (201 rows)
- [Processed Excel Table.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/Processed%20Excel%20Table.xlsx) — data after processing (equipment + generated location assets)
- [Transcript Index](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/transcript-index-373) — 16 meeting transcripts linked to #373
- Countries Data enum (provided by Rein Feb 11, 249 entries) — country name matching reference

**Session:**
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/1443f1aa-f108-4e27-94ba-d2954cc26dc5.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/6b1217f1-1b02-434e-be24-22dd1a8a1975.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/494332f2-3f40-4c9b-9d96-43e84c3f2441.jsonl`
