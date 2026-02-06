---
publish: true
---

# Chain 1B: Step 3 Fill Level-by-Level - Design Document
[[client-archibus]]

## Problem Statement

**The gap between client data and system schema is the implementer's burden.**

Populating Bruce BEM's background data requires understanding the client's hierarchy, mapping their columns to schema fields, and filling data level-by-level. AI takes over this process; the implementer only resolves what AI cannot.

**Key Assumptions:**
- Input is typically an **equipment table** (rows represent equipment, not locations)
- **Hierarchy is embedded in columns** (Building, Floor, Room as text values)
- **Location-based assets must be created** from these column values (they don't exist as rows)

---

## High-Level Overview

```
INPUT                    PROCESS                           OUTPUT
─────                    ───────                           ──────
Equipment Excel    →     1. Pick highest level        →    Hierarchical JSON
(hierarchy in            2. Group children                 (id: null)
columns)                 3. Extract unique values          ↓
                         4. Create parent assets      →    Bruce BEM
                         5. Build JSON                     (PKs assigned)
```

---

## Success Definition

| Element | Definition |
|---------|------------|
| Goal | Fill Bruce BEM schema from client Excel |
| Success | Hierarchical assets populated correctly |
| Failure mode | Actionable - API returns error, AI fixes |
| AI role | Structure + transformation + error handling |
| Implementer role | Confirms hierarchy, validates mappings |

---

## Step 3 Process (Detailed)

**For EACH highest-level element** (e.g., Building A, B, C, D) **and all its children**:

| Phase | Action | Example |
|-------|--------|---------|
| **Filter** | Find all rows where column = highest value | 49 rows have "Building A" in Col D |
| **Extract hierarchy** | Each row contains full parent chain | Row: Building A → Floor 3 → Room 333 |
| **Deduplicate** | Extract unique values per level | 6 unique floors, 47 unique rooms |
| **Handle multi-parent** | Same room on different floors = separate assets | Room 381 on Floor G ≠ Room 381 on Floor 3 |
| **Create parents** | Generate location-based asset rows | 1 Building + 6 Floors + 47 Rooms = 54 assets |
| **Build JSON** | Nested structure, Bruce BEM field names, all IDs null | 1-to-N at each level |
| **Insert** | API traverses top-down, assigns PKs | Stop at first error |
| **Repeat** | Next highest-level element | Building B, C, D... |

---

## Decisions Made (Feb 3)

| Decision | What | Rationale |
|----------|------|-----------|
| **Tree-walk** | Process one highest-level element and all its children at a time | API handles PKs, AI doesn't track |
| **Stop at first error** | API halts, returns error | AI focuses on one issue |
| **Sequential first** | No parallel processing in PoC | Simplicity |
| **AI creates JSON** | With Bruce BEM field names | Step 2 mapping = transformation rule |
| **Quality over minimum** | Recommended fields for location assets | Better data at onboarding |

---

## Data Transformation

| Level | What | Example |
|-------|------|---------|
| **Column-level** (Step 2) | Map input column → Bruce BEM field | "Asset Name" → `Name` |
| **Cell-level** (Step 3) | Transform values | "Air Con" → `"HVAC"`, dates → ISO |

**Transformation types:**
- Enum normalization
- Date formatting
- Type coercion
- Null handling
- Hierarchy resolution → ParentId

---

## JSON Output

**Core AI value:** JSON follows Bruce BEM's asset data schema exactly. API can insert directly without transformation.

```json
{
  "building": {
    "id": null, "parent_id": null,
    "Name": "Building A",
    "AssetType": "Building",
    "floors": [{
      "id": null, "parent_id": null,
      "Name": "Floor 3",
      "AssetType": "Floor",
      "rooms": [{
        "id": null, "parent_id": null,
        "Name": "Room 333",
        "AssetType": "Room",
        "equipment": [{
          "id": null, "parent_id": null,
          "Name": "HVAC-001",
          "AssetType": "HVAC",
          "SerialNumber": "SN000001"
        }]
      }]
    }]
  }
}
```

**Why this matters:**
- Step 2 mapping → AI knows target schema
- JSON uses exact Bruce BEM field names
- API inserts without additional work
- All IDs null → API assigns PKs on insert

---

## Open Items

| Category | Item | Status |
|----------|------|--------|
| **Step 1 UX** | Hierarchy input method | Pending FM meeting |
| **Step 1 UX** | Who defines UX | Kaya/Mujahid/Ali |
| **Implementation** | "Floor All" handling | Open |
| **Implementation** | Missing hierarchy columns | Open |
| **Implementation** | Empty cells in hierarchy | Open |
| **Implementation** | Property level above Building | Open |

---

## API Assumptions

| Assumption | Description |
|------------|-------------|
| Accepts nested JSON | Or flat variant |
| Top-down traversal | Parents before children |
| Returns meaningful errors | Which item, why |
| Stop at first error | No batch errors |
| **Doesn't exist yet** | Rein builds this |

---

## Source

**Transcripts:**
- Feb 3 - Rein meeting (hierarchy validation, JSON schema)
- Feb 3 - Miguel/Ian meeting (bulk data entry prototype)
- Feb 5 - Ryan & Miguel meeting (key design decisions)

**Artifacts:**
- Rein's processed Excel (ExcelTableAfterProcessingByAI.xlsx)
- CAFM Asset Sample
- [Asset Import Schema (SharePoint)](https://aschs-my.sharepoint.com/:x:/g/personal/rein_suurvali_asc-hs_com/IQCZWnERRmHxRb3r4bb3BZzRAfjwreav4FEP85btLlaVWNI)

**Session:**
- Rubber-duck: `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/39c514f1-a97f-4d30-b04c-17d8592d2992.jsonl`
