---
publish: true
date: 2026-01-19
source: Dec 30 meeting synthesis + Jan 19 decomposition session
status: draft
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

```
INPUT (messy Excel) → MAPPING → SCHEMA (backpressure) → OUTPUT (accepted + rejected)
                                    ↑
                              AI iterates here
```

## Success Definition

> "An AI-powered feedback loop that fills background data schema as much as possible from client inputs, flags what it cannot fill, thereby freeing partner time."

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

### Component 1: Schema (BLOCKED)

**What it is:** Bruce BEM background data tables

**What we know:**
- "Background data" = spatial drill down + assets + people
- Spatial hierarchy: site → building → floor → room → desk
- Bruce BEM has different structure than Archibus (hierarchy in same table vs separate)

**What we need:** List of Bruce BEM tables for background data

**Action:** Rein session with questions:
1. What are the Bruce BEM background data tables?
2. What insertion mechanisms does Bruce BEM support?

**Why this matters:** Schema IS the backpressure mechanism. Without it, the loop has nothing to validate against.

### Component 2: Inputs (PARTIAL)

**What it is:** Sample client Excel files

**What we know:**
- Clients give Excel (inconsistent formats)
- Column names vary (equipment_code vs eq_id vs asset_id)
- Data often incomplete
- Format unpredictable

**What we need:** Real client data OR synthetic test data

**Action:** Contact Ali/Mujahid for sample Excel (any client works)

**Fallback:** Create synthetic test data

### Component 3: Mapping (BLOCKED by 1+2)

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

**Status:** Design after schema + input available

### Component 4: Validation Rules (BLOCKED by 1)

**Two types of correctness:**

| Type | Question | Can automate? |
|------|----------|---------------|
| STRUCTURE | Is format right? | Yes (schema tells us) |
| VALUE | Is content right? | Harder (needs domain knowledge) |

**Structure validation:** Derive from schema (types, required fields)

**Value validation:** Lookup existing data, ask human, contextual inference

**Action:** Clarify validation rules when discussing schema with Rein

### Component 5: Hierarchy → Context Engineering

**Original framing:** Hierarchy enforcement (site → building → floor → room → asset)

**Refined framing:** Hierarchy IS a property of schema (FK relationships). The real question is **context engineering** - how do we encode schema into AI?

**Two approaches:**
- A: Let AI try (trial and error)
- B: Encode schema into AI context (pre-load knowledge)

**Hypothesis:** Background data tables are KNOWN and STABLE → encode into AI context

### Components 6-9: Design Decisions (After Prototype)

| # | Component | Decision Type | Options |
|---|-----------|---------------|---------|
| 6 | Exception Handling | UX | Silent flag / Ask immediately / Batch report |
| 7 | Question Generation | AI Design | Every uncertainty / Only blockers / Suggest+confirm |
| 8 | Output Format | UX | JSON / Excel / Dashboard / Report |
| 9 | Loop Mechanism | Architecture | Batch / Streaming / Interactive |

These are implementation-specific. Decide during prototyping.

## Dependencies

```
Schema (Rein) ──┬──→ Mapping
                │
Input (Ali) ────┘
                │
                ↓
        Context Engineering
                │
                ↓
           Prototype
                │
                ↓
      Design Decisions (6-9)
```

**Critical path:** Schema unlocks everything.

## Action Plan

1. **Schedule Rein session**
   - Q1: What are Bruce BEM background data tables?
   - Q2: What insertion mechanisms does Bruce BEM support?

2. **Get sample input** (parallel)
   - Contact Ali/Mujahid for real client Excel
   - Fallback: synthetic test data

3. **Create summary document** → Share for team alignment

4. **Design context engineering** → How AI uses schema

5. **Prototype the loop**

## Related Issues

- [#373: AI Bulk Data Entry Prototype for FMM Use Case](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- [#267: Value Discovery Methodology](https://github.com/DaveX2001/deliverable-tracking/issues/267)
- [#146: AI querying large SQL datasets](https://github.com/DaveX2001/deliverable-tracking/issues/146) (related: insertion mechanisms)

## Meeting Sources

- Dec 18: Pattern encoding session (7 chains identified)
- Dec 23: Chain prioritization (1B → 3 → 7 sequence)
- Dec 30: Bulk Entry Spike (workflow definition, hierarchy constraint)
- Jan 19: Component decomposition session
