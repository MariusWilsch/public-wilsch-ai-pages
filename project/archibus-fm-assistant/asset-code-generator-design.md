---
publish: true
---

# Asset Code Generator - Design Doc
[[client-archibus]]

## Problem Statement

**Repetitive manual work generating asset codes from raw Excel data.**

Partners receive client Excel files with raw asset data (building, floor, room, equipment type). For each asset, they must manually construct an Equipment Code following the pattern: `{Building}-{Floor}-{Room}-{Type}-{Sequence}`. This involves:

1. Transforming building codes (strip prefixes)
2. Truncating room codes
3. Mapping equipment types to abbreviations
4. Tracking sequence numbers

This is tedious, error-prone work repeated for every FM implementation.

---

## Success Definition

**A tool that generates Equipment Codes from raw Excel data, reducing manual effort and errors.**

| Element | Definition |
|---------|------------|
| Goal | Generate Equipment Codes for all rows with sufficient data |
| Success | Excel file with Equipment Code column populated correctly |
| Failure mode | Actionable - tool flags rows it cannot process with clear reason |

---

## Target Audience

**Primary: FM Implementation Partners**
- Repeatable pain (every implementation)
- Currently manual, time-consuming work
- Direct beneficiaries of automation

**Secondary: Clients**
- Faster onboarding
- Consistent asset naming

---

## High-Level Overview

```
INPUT                    STEPS                         OUTPUT
─────                    ─────                         ──────
Excel file          →    1. Read columns               →    Excel file with
with raw data            2. Transform each segment          Equipment Codes
                         3. Track sequences                 filled in
                         4. Generate codes
```

**Equipment Code Pattern:** `{Building}-{Floor}-{Room}-{Type}-{Sequence}`

**Example:** `DUP-GF-LRBA-ACO-0001`

| Segment | Source Column | Transformation | Example |
|---------|---------------|----------------|---------|
| Building | Column E | Strip prefix (A-DUP → DUP) | A-DUP → DUP |
| Floor | Column G | Use as-is | GF → GF |
| Room | Column F | First 4 chars | LRBAL → LRBA |
| Type | Column D | Abbreviate | AIR-CON → ACO |
| Sequence | Generated | Continuous counter | 0001, 0002... |

---

## Key Uncertainties

### Uncertainty #1: Schema Consistency

**Core question:** Can we expect a consistent input schema, or will columns vary?

**What "schema" means:**

| Aspect | Question | Example |
|--------|----------|---------|
| Column index | Will Building Code always be in Column E? | Or could it be Column C in another file? |
| Header name | Will the header always say "Building Code"? | Or could it be "Site", "Block", "Location"? |

**Why this matters:** If every client file looks different, the tool needs to handle that variation. If files are mostly consistent, it's simpler.

**Question:** How similar are client Excel files? Same columns in same places, or does every file look different?

---

### Uncertainty #2: Data Availability & Value Consistency

**Two dimensions:**

| Dimension | Question |
|-----------|----------|
| Presence | Are all required fields populated? |
| Format | Do values follow a consistent pattern? |

**Required fields:** Building Code, Floor Code, Room Code, Equipment Standard

**Format examples:**

| Field | Consistent | Inconsistent |
|-------|------------|--------------|
| Building Code | Always like `A-DUP`, `V01`, `F704` | Could be "Main Building", "Block A", "HQ" |
| Floor Code | Always like `GF`, `1F`, `7F` | Could be "Ground", "First", "Level 1" |
| Room Code | Always like `LRBAL`, `KIT`, `BED1` | Could be "Living Room", "Kitchen", "Bedroom" |
| Equipment Standard | Always like `AIR-CON`, `ELEC-LGHT` | Could be "Air Conditioner", "Light Fixture" |

**Questions:**
1. Do clients use short codes like `KIT` or write out full words like "Kitchen"?
2. How common are rows missing one of the four segments (Building, Floor, Room, Type)?

---

### Uncertainty #3: Transformation Rules

**Core question:** Are transformation rules standardized or client-specific?

| Segment | Transformation | Question |
|---------|---------------|----------|
| Building | Strip prefix? | Why strip `A-` from `A-DUP`? Universal rule? |
| Floor | Use as-is? | Or does it need transformation? |
| Room | Truncate to 4? | Always 4 chars? Pad if shorter? |
| Type | Abbreviate | Who defines the abbreviation table? |
| Sequence | Continuous? | Or reset per combo? |

**Impact:**

| If... | Then... |
|-------|---------|
| Rules are universal | Encode once, use everywhere |
| Rules are client-specific | Need configuration per project |

**Question:** Are these transformation rules used EVERYWHERE, or did this client just happen to use these rules?

---

### Uncertainty #4: Output Format

**Core question:** What should the tool produce?

| Option | Description |
|--------|-------------|
| Same file (in-place) | Update Equipment Code column in original Excel |
| New file | Create separate output file, preserve original |
| Preview first | Show codes before saving, user approves |

**Question:** What workflow do partners expect?

---

### Uncertainty #5: Abbreviation Uniqueness

**Core question:** Must each Equipment Standard have a unique abbreviation?

**The collision problem:**
- `ELEC-LGHT`, `ELEC-OVE`, `ELEC-REFRI`, `ELEC-WASH` → all map to `ELE` if using first 3 chars
- Same with `FURN-*` types → all map to `FUR`

| If collisions are OK | If must be unique |
|----------------------|-------------------|
| Two different equipment types can have same abbreviation | Need distinct abbreviation for each type |
| Sequence number is the differentiator | Type can be identified from code alone |

**Question:** Does each equipment type need its own unique abbreviation?

---

## Example Output

Sample Excel updated with generated Equipment Codes:

```
Equipment Code           Equipment Standard  Building  Room   Floor
────────────────────────────────────────────────────────────────────
DUP-GF-LRBA-ACO-0001    AIR-CON            A-DUP     LRBAL  GF
DUP-GF-KIT-ACO-0002     AIR-CON            A-DUP     KIT    GF
DUP-1F-BED1-ACO-0003    AIR-CON            A-DUP     BED1   1F
V01-GF-BED1-ACO-0005    AIR-CON            V01       BED1   GF
DUP-GF-LRBA-ELE-0009    ELEC-LGHT          A-DUP     LRBAL  GF
```

Full example file: `/Users/verdant/Downloads/Sample Asset Data (1).xlsx`

---

## Next Step

Before building, one core question: **How standardized is the data we'll receive?**

- If data is consistent → straightforward tool
- If data varies wildly → need more design work first

---

## Source

- Mujahid <> Marius meeting (2026-01-22)
- Transcript: [Fireflies 01KFJY8NN704JPPSY8TK405S1B](https://app.fireflies.ai/view/01KFJY8NN704JPPSY8TK405S1B)
- Issue: [#605 - AI-Assisted Asset Code Generation](https://github.com/DaveX2001/deliverable-tracking/issues/605)
