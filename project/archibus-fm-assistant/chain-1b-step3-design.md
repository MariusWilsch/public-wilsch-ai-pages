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

| Element | Definition |
|---------|-----------|
| **Goal** | Equipment table transformed into hierarchical JSON that Bruce BEM can insert, with self-correction on API errors |
| **Success** | For each top-level hierarchy element (e.g., 4 buildings = 4 JSONs): generate nested JSON → insert via API → if error, AI corrects and retries → move to next element. All elements populated. |
| **Done test** | "Can I write a meeting agenda with open design questions about Step 3?" → If NO → design complete |

---

## Approach

### 1. Filter by Top-Level Element — ✅ Defined

For each unique value at the highest hierarchy level (e.g., Building A, B, C, D), process one element and all its children at a time. 4 buildings = 4 sequential processing runs.

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW), [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ)*

### 2. Extract Hierarchy Chain — ✅ Defined

Each equipment row contains its full parent chain embedded in columns (Building A → Floor 3 → Room 333). Extract this chain per row.

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW)*

### 3. Deduplicate Per Level — ✅ Defined

Extract unique values at each hierarchy level. 49 equipment rows might yield 1 building, 6 unique floors, 47 unique rooms. Same room name on different floors = separate assets — identity is by parent relationship, not by name alone (Room 308 on Floor G ≠ Room 308 on Floor 3).

*Source: [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ)*

### 4. Create Location-Based Assets — ⚠️ Partially Defined

Location assets (buildings, floors, rooms) don't exist as rows in the input — they must be created from column values. Equipment attaches to the lowest known hierarchy level. If a level is empty (no room), equipment attaches one level up (floor).

**Undefined:** Floor All handling — where does building-wide equipment (elevators, fire systems) attach in the hierarchy? → *[Meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step3-field-alignment)*

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW) (Rein: "equipment parent is the room... when the room is empty, it goes to the next level")*

### 5. Build Nested JSON — ⚠️ Partially Defined

Required fields per asset: `Name`, `AssetType`, `ParentId`. All IDs null — API assigns PKs on insert. Asset codes optional. Full field specification available (37 fields with types, lengths, requirements — see [AssetImportDescription on SharePoint](https://aschs-my.sharepoint.com/:x:/g/personal/rein_suurvali_asc-hs_com/IQCZWnERRmHxRb3r4bb3BZzRAfjwreav4FEP85btLlaVWNI)).

**Undefined:** Field naming — ImportTemplate uses PascalCase (`Name`, `AssetType`), Rein's Hierarchical Body.json uses lowercase (`name`, `type`). Which does the insertion API expect? → *[Meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step3-field-alignment)*

**Undefined:** Country name → Country ID mapping — Bruce BEM stores country IDs, Excel has country names. Who resolves? → *[Meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step3-field-alignment)*

*Source: [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ), [AssetImportDescription.xlsx](https://aschs-my.sharepoint.com/:x:/g/personal/rein_suurvali_asc-hs_com/IQCZWnERRmHxRb3r4bb3BZzRAfjwreav4FEP85btLlaVWNI)*

### 6. Insert via API + Backpressure — ⚠️ Partially Defined

Send JSON for one top-level element. API traverses top-down (parents before children). Stop at first error. AI reads error response, corrects JSON, retries. Sequential processing — no parallel in PoC.

**Dependent on:** Rein's insertion API (does not exist yet). Error response format undefined until API is built.

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW) (decisions: tree-walk, stop at first error, sequential)*

### 7. Repeat — ✅ Defined

Move to next top-level element. Continue until all elements processed.

---

## Decisions

| Decision | What | Date | Source |
|----------|------|------|--------|
| **Tree-walk** | Process one top-level element and all children at a time | Feb 3 | Rein meeting |
| **Stop at first error** | API halts, returns error for AI to correct | Feb 3 | Rein meeting |
| **Sequential first** | No parallel processing in PoC | Feb 3 | Rein meeting |
| **AI creates JSON** | With Bruce BEM field names from Step 2 mapping | Feb 3 | Rein meeting |
| **No interactive editing** | AI proposes/processes hierarchy, doesn't modify Excel | Feb 3 | Rein meeting |
| **Asset codes optional** | Not required by API. Don't generate unless client wants. | Feb 6 | Rein: "for me they are not important" |
| **All IDs null** | API assigns PKs on insert. Confirmed explicitly. | Feb 6 | Rein: "I don't need these IDs at all" |
| **Lowest level attachment** | Equipment attaches to lowest known hierarchy level. Empty = fallback up. | Feb 3 | Rein: "equipment parent is the room... when empty, goes to next level" |

---

## Source

**Artifacts:**
- [Asset Import Schema (SharePoint)](https://aschs-my.sharepoint.com/:x:/g/personal/rein_suurvali_asc-hs_com/IQCZWnERRmHxRb3r4bb3BZzRAfjwreav4FEP85btLlaVWNI) — field names, types, requirements
- `.claude/tracking/issue-373/` — reference files (AssetImportDescription.xlsx, asset_types, Hierarchical Body.json, Processed Excel Table)
- [CAFM Asset Upload Sample](https://github.com/veloxforce/claude-user-configs/raw/main/hippocampus/project/archibus-fm-assistant/cafm-asset-upload-sample-2026-01-28.xlsx)

**Session:**
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/1443f1aa-f108-4e27-94ba-d2954cc26dc5.jsonl`
