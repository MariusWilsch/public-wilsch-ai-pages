---
publish: true
---

# Chain 1B: AI Bulk Data Entry - Design Document v2
[[client-archibus]]

## Problem Statement

**The gap between client data and system schema is the implementer's burden.**

Populating Bruce BEM's background data requires understanding the client's hierarchy, mapping their columns to schema fields (35 fields for the asset table alone), and filling data level-by-level — fixing validation errors at each step. Today the implementer carries this end-to-end. AI takes over the process; the implementer only resolves what AI cannot.

**The process requires three steps:**
1. **Understand the hierarchy** — which levels does this client use? (subset of 9 possible)
2. **Map the input column schema to the background table schema** — client column names → Bruce BEM schema fields
3. **Fill level-by-level** — parents must exist before children, resolving gaps row by row

**Key Assumptions:**
- Input is typically an **equipment table** (rows represent equipment, not locations)
- **Hierarchy is embedded in columns** (Building, Floor, Room as text values)
- **Location-based assets must be created** from these column values (they don't exist as rows)

---

## Decisions Made (Feb 3 Session)

### Decision 1: Insertion Order — Tree-Walk

**Decided:** Tree-walk per highest-level element.

| Step | Action |
|------|--------|
| 1 | Pick highest-level value (e.g., "Building A") |
| 2 | Find all rows referencing that value |
| 3 | Extract unique values at each child level |
| 4 | Create hierarchical JSON |
| 5 | Insert via API (top-down) |
| 6 | Repeat for next highest-level value |

**Why tree-walk:** AI creates hierarchical JSON with `id: null`, `parent_id: null`. API handles PK assignment as it traverses top-down. AI doesn't track PKs.

---

### Decision 2: Error Handling — Stop at First Error

**Decided:** API stops at first error, returns it to AI.

**Motivation:**
- AI focuses on one issue at a time (not overwhelmed)
- No complex error collection logic
- Vertical slice approach — complete one path before moving on

**Trade-off:** More network roundtrips, but simpler mental model. Acceptable for initial implementation.

---

### Decision 3: Parallel Processing — Sequential First

**Decided:** Sequential for PoC. Parallel is future optimization.

---

### Decision 4: Responsibilities Split

| Component | Responsibilities |
|-----------|------------------|
| **AI** | Create JSON structure with **Bruce BEM field names** |
| **AI** | Map input columns → Bruce BEM fields (Step 2) |
| **AI** | Transform cell values (types, enums, dates) |
| **AI** | Handle errors, fix and retry |
| **API** | Assign PKs (id, parent_id) |
| **API** | Validate against schema |
| **API** | Return meaningful errors |

**Key clarification:** Step 2 mapping is the **transformation rule** for JSON output. JSON must use Bruce BEM's exact field names.

---

### Decision 5: Recommended Minimum Columns

**Principle:** Quality over minimum at onboarding.

| Asset Type | Source | Recommended Minimum |
|------------|--------|---------------------|
| **Location-based** (Building, Floor, Room) | AI creates from column values | Name, AssetType, OwnerId + context fields (Address, City) |
| **Equipment** | Original input rows | Full row data via column mapping |

**Why:** Easier to capture quality data at onboarding than fix later.

---

## Data Transformation

### Column-Level (Step 2)

Step 2 produces mapping: `"Asset Name" → Name`, `"Serial No." → SerialNumber`

- AI identifies mappings semantically
- Implementer confirms
- JSON must use Bruce BEM field names (right side)

### Cell-Level (Step 3)

Step 3 applies mapping and transforms values:

| Transformation | Input | Output |
|----------------|-------|--------|
| Column name | "Asset Name" | `Name` field |
| Enum normalization | "Air Con" | `"HVAC"` |
| Date formatting | "15/01/2024" | `"2024-01-15"` |
| Type coercion | "123.5 sqm" | `123.5` |
| Null handling | "" or "N/A" | `null` |
| Hierarchy resolution | "Floor 3" | Nested structure + `ParentId` |

---

## JSON Output Structure

AI creates hierarchical JSON with all IDs null:

```json
{
  "building": {
    "id": null,
    "parent_id": null,
    "Name": "Building A",
    "AssetType": "Building",
    "floors": [
      {
        "id": null,
        "parent_id": null,
        "Name": "Floor 3",
        "AssetType": "Floor",
        "rooms": [
          {
            "id": null,
            "parent_id": null,
            "Name": "Room 333",
            "AssetType": "Room",
            "equipment": [
              {
                "id": null,
                "parent_id": null,
                "Name": "HVAC-001",
                "AssetType": "HVAC",
                "SerialNumber": "SN000001"
              }
            ]
          }
        ]
      }
    ]
  }
}
```

**API traverses top-down, assigns PKs, fills parent_id automatically.**

---

## Open Items

### Step 1 UX (Pending FM Expert Meeting)

| Question | Status |
|----------|--------|
| How does implementer tell AI the hierarchy? | Open (chat vs UI vs file) |
| Who defines this UX? | Open (Kaya/Mujahid/Ali) |
| What if hierarchy data is missing? | Open (AI warns, implementer fills) |

See: [Step 1 Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/miguel-ian-meeting-agenda-hierarchy-understanding)

### Implementation-Specific

| Item | Description | Status |
|------|-------------|--------|
| "Floor All" | Touches all floors | Open |
| Multi-floor rooms | Same room on multiple floors = separate assets | Noted |
| Column name variations | "Building" vs "Location" | AI handles |
| Value inconsistencies | "Building A" vs "Bldg A" | AI handles |
| Missing hierarchy columns | What if Floor doesn't exist? | Open |
| Empty cells | Null handling in hierarchy | Open |
| Property level | When to add above Building | Open |

---

## API Assumptions

| Assumption | Description |
|------------|-------------|
| Accepts nested JSON | Structure above (or flat variant) |
| Top-down traversal | Inserts parents before children |
| Meaningful errors | Returns which item failed and why |
| Stop at first error | Doesn't continue after failure |
| Bruce BEM schema | Validates against existing rules |
| **Doesn't exist yet** | Rein needs to build this |

---

## References

- [CAFM Asset Sample](https://github.com/veloxforce/claude-user-configs/raw/main/hippocampus/project/archibus-fm-assistant/cafm-asset-upload-sample-2026-01-28.xlsx)
- [Rein's Processed Excel](ExcelTableAfterProcessingByAI.xlsx)
- [Asset Import Schema (SharePoint)](https://aschs-my.sharepoint.com/:x:/g/personal/rein_suurvali_asc-hs_com/IQCZWnERRmHxRb3r4bb3BZzRAfjwreav4FEP85btLlaVWNI)
- Rubber-duck session: `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/39c514f1-a97f-4d30-b04c-17d8592d2992.jsonl`
