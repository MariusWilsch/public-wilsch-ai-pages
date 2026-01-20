---
publish: true
---

# Chain 1B: AI Bulk Data Entry - Design Document
[[client-archibus]]

## Problem Statement

**Low margin + high effort at database inception.**

Partners spend excessive time formatting client data for onboarding. Current process:
1. Client gives raw Excel (inconsistent columns, minimal data)
2. Partner manually reformats into multi-tabbed spreadsheet
3. Manual validation via macros (data types, required fields)
4. Upload in correct hierarchy order (site → building → floor → room → asset)
5. Exception handling when foreign keys missing

This is low-margin, repetitive work that holds up implementations.

## Mental Model: Backpressure

**Schema as automated feedback mechanism.**

From [Don't Waste Your Backpressure](https://banay.me/dont-waste-your-backpressure/):
- Backpressure = automated feedback that lets AI self-correct without human intervention
- Expressive constraints make invalid states unrepresentable
- Quality error reporting guides correction
- Automated verification loops enable autonomous iteration

**In this project:**
- **Schema = the backpressure mechanism** (accepts/rejects with actionable reason)
- **Mapping = connecting input TO the backpressure mechanism**
- **Validation = executing the backpressure** (the actual accept/reject loop)

<img src="chain-1b-flow.png" width="600" alt="Chain 1B Flow">

## Success Definition

> "An AI-powered feedback loop that fills background data schema as much as possible from client inputs, asks where uncertain, flags what it cannot fill, thereby freeing partner time."

| Element | Definition |
|---------|------------|
| Goal | Fill schema as much as possible (not perfection) |
| Success | Background data tables populated |
| Failure mode | Actionable - AI knows WHY data was rejected |
| Human role | Resolve what AI cannot (decision authority) |

**Key insight from Ian:**
> "It doesn't mean that the data has to be 100% complete. The principle is if we can speed up that data entry and validation process..."

## Target Audience

**Primary: Partners/Implementers**
- Repeatable pain (every implementation)
- Low-margin work that AI can make profitable
- Bottleneck that holds up implementations

**Secondary: End-users**
- Benefit from faster onboarding
- Cleaner data in system
- One-time pain (their onboarding)

## Component Decomposition

### Component 1: Schema

**What it is:** Bruce BEM background data tables

**Characteristics:**
- "Background data" = spatial drill down + assets + people
- Spatial hierarchy: site → building → floor → room → desk
- Bruce BEM has different structure than Archibus (hierarchy in same table vs separate)

**Requirements:**
- List of Bruce BEM background data tables
- Insertion mechanisms Bruce BEM supports

**Why this matters:** Schema IS the backpressure mechanism. Without it, the loop has nothing to validate against.

### Component 2: Inputs

**What it is:** Sample client Excel files

**Characteristics:**
- Clients give Excel (inconsistent formats)
- Column names vary (equipment_code vs eq_id vs asset_id)
- Data often incomplete
- Format unpredictable

**Requirements:** Real client data OR synthetic test data

### Component 3: Mapping

**What it is:** Connecting messy input columns → schema fields

**This is the core problem:**
- Fuzzy matching (equipment_code ≈ eq_id)
- Semantic understanding (description → name/desc field)
- When uncertain → ASK human

**Sub-layers:**
1. Column name matching
2. Data type matching
3. Semantic matching
4. Hierarchy detection (is this a Site or Building?)
5. Missing field handling

**Dependencies:** Requires schema + input

### Component 4: Validation Rules

**Two types of correctness:**

| Type | Question | Can automate? |
|------|----------|---------------|
| STRUCTURE | Is format right? | Yes (schema tells us) |
| VALUE | Is content right? | Harder (needs domain knowledge) |

**Structure validation:** Derive from schema (types, required fields)

**Value validation:** Lookup existing data, ask human, contextual inference

### Component 5: Context Engineering

**Hierarchy IS a property of schema** (FK relationships). The real question is how to encode schema into AI context.

**Two approaches:**
- A: Let AI try (trial and error)
- B: Encode schema into AI context (pre-load knowledge)

**Hypothesis:** Background data tables are KNOWN and STABLE → encode into AI context

### Components 6-9: Design Decisions

| # | Component | Decision Type | Options |
|---|-----------|---------------|---------|
| 6 | Exception Handling | UX | Silent flag / Ask immediately / Batch report |
| 7 | Question Generation | AI Design | Every uncertainty / Only blockers / Suggest+confirm |
| 8 | Output Format | UX | JSON / Excel / Dashboard / Report |
| 9 | Loop Mechanism | Architecture | Batch / Streaming / Interactive |

These are implementation-specific.

## Related Issues

- [#373: AI Bulk Data Entry Prototype for FMM Use Case](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- [#267: Value Discovery Methodology](https://github.com/DaveX2001/deliverable-tracking/issues/267)
- [#146: AI querying large SQL datasets](https://github.com/DaveX2001/deliverable-tracking/issues/146) (related: insertion mechanisms)
