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

For each unique value at the highest hierarchy level, process one element and all its children at a time. This scopes the work: instead of transforming 201 rows at once, AI handles one building's worth (~18-80 rows) per run.

```
201 equipment rows (4 buildings)
    │
    ├─→ Run 1: Building A (18 rows) ← process this first
    ├─→ Run 2: Building B (~50 rows)
    ├─→ Run 3: Building C (~50 rows)
    └─→ Run 4: Building D (~80 rows)
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

**Assumption:** Step 1 has already confirmed the hierarchy structure (e.g., Building → Floor → Room). The hierarchy is embedded in the row — we do not infer or create it. The client provides the data with hierarchy columns already present.

**Example — chains from the 3 Building A rows above:**

```
A008: Building A → Floor All → Room 116
A010: Building A → Floor 3   → Room 333
A034: Building A → Floor 1   → Room 194
```

The chain determines where each equipment asset will attach in the hierarchy. Note: A008 has Floor = "All" — an edge case resolved in Element 4.

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

**Identity rule:** Room identity = parent + name. Room 333 under Floor 3 is a different asset than a hypothetical Room 333 under Floor 2.

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

The hierarchy is expressed in the nested JSON structure (Element 5), not via explicit Parent Codes. Equipment attaches to the lowest known hierarchy level. If a level is empty (no room), equipment attaches one level up (floor).

**Reference:** [asset_types 1.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset_types%201.xlsx) — predefined AssetType enum (includes Building, Floor, Room).

**Undefined:** Floor All handling — where does building-wide equipment (Floor = "All") attach? → *[Meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step3-field-alignment)*

**Undefined:** Multi-level emptiness — recursive fallback confirmation → *[Meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step3-field-alignment)*

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW) (Rein: "equipment parent is the room... when the room is empty, it goes to the next level")*

### 5. Build Nested JSON

Transform the flat table (equipment rows + generated location assets from Element 4) into nested JSON that the insertion API can accept. Every asset follows the same schema — location assets and equipment are all rows in the same `assets` table, differentiated by AssetType.

#### Field Tiers

| Tier | Fields | Rule |
|------|--------|------|
| **Required (AI-filled)** | Name, AssetType | Must be in JSON. Insert fails without them. |
| **Filled by Bruce** | Id, ParentId, OwnerId | Leave null — system auto-assigns. |
| **Optional (AI-filled)** | All other 31 fields | Include only if the source data provides them. AI maps, doesn't fabricate. |

**Reference:** [AssetImportDescription.xlsx → "Fields Assets" sheet](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/AssetImportDescription.xlsx) — full 36-field schema with data types, lengths, and requirements.

#### The Transform

AI performs four sub-operations on each row:

1. **Column mapping** — which Excel column feeds which JSON field (Step 2 output)
2. **Value transformation** — enum matching, date format conversion, country resolution
3. **Structure transformation** — flat rows → nested tree (children inside parents)
4. **Missing value handling** — empty cells → behavior TBD (omit, null, or default)

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
    4. Missing value handling (empty cells → TBD)
    ↓
AI-Generated JSON node
    ↓
Element 6: Validate via API
    ↓
API accepts ✅  OR  API rejects ❌ → error → AI corrects → resubmit
```

**Undefined:** Field naming — AssetImportDescription uses PascalCase (`Name`, `AssetType`), Hierarchical Body.json uses lowercase (`name`, `type`). Which does the new API expect? → *[Meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step3-field-alignment)*

**Undefined:** Country name → Country ID mapping — does the API accept names or require IDs? → *[Meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step3-field-alignment)*

**Undefined:** New API fields — does the insertion API accept `manufacturer` and `model` beyond the 36-field ImportTemplate? → *[Meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step3-field-alignment)*

**Undefined:** Missing value handling — empty cells: omit field, send null, or API default? → *[Meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step3-field-alignment)*

*Source: [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ), [AssetImportDescription.xlsx](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/AssetImportDescription.xlsx)*

### 6. Insert via API + Backpressure

Send the AI-generated JSON for one top-level element to the insertion API. Sequential processing — no parallel in PoC.

**Proposed insertion model (validate-then-insert):**

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

Nothing is inserted until the entire payload validates. No partial state.

**Proposed error response (example — actual format to be defined with Rein during API development):**

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

**Implementation boundary:** Elements 1–5 are implementable now (AI-generated JSON production). Element 6 requires Rein's insertion API, which does not exist yet.

**Reference:** API specification TBD — to be provided by Rein.

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW) (decisions: tree-walk, stop at first error, sequential)*

### 7. Repeat

Move to next top-level element. Continue until all elements processed.

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

**Session:**
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/1443f1aa-f108-4e27-94ba-d2954cc26dc5.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/6b1217f1-1b02-434e-be24-22dd1a8a1975.jsonl`
