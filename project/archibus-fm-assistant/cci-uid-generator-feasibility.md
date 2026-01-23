---
publish: true
---

# CCI UID Generator - Feasibility Assessment
[[client-archibus]]

**Status:** Assessment Complete | **Date:** 2026-01-23 | **Issue:** [#591](https://github.com/DaveX2001/deliverable-tracking/issues/591)

## Context

Kaia asked if AI could automatically generate unique IDs for building assets based on CCI (Construction Classification International) rules. Currently this is done manually by looking up classification tables.

## What is CCI?

CCI-EE is Estonia's construction classification system based on ISO standards. It provides a unified way to label buildings, systems, and components across the construction lifecycle.

**Code Structure:**
```
CO-GPB01%GPB03(DA01;5;30;75)
│  │     │     └── Properties (flowrate, pressure, efficiency)
│  │     └── Type code (specific variant)
│  └── Class code (pump category)
└── Table prefix (CO = Component)
```

**Classification Tables:**

| Code | What it classifies |
| ---- | ------------------ |
| CO | Components (pumps, doors, equipment) |
| CS | Spaces (rooms, corridors) |
| CE | Buildings |
| CF/CT | Systems (functional/technical) |

## Feasibility Verdict

**YES - Automation is feasible.**

### Why it's deterministic

1. **Table lookup is mechanical:** "Centrifugal pump" → GPB (no judgment needed)
2. **616 component codes** in CO table - finite, well-defined mappings
3. **Hierarchical structure:** Category → Subcategory → Specific (3 levels)
4. **ISO-based rules:** Clear standards, not ad-hoc decisions

### Why AI could do this

Given structured input, AI can:
1. Identify asset type → select correct table (CO for components)
2. Map asset name to class code → lookup in table
3. Add type/variant code → if specified
4. Append properties → if provided

## Open Question: Input Structure

**We don't know what data Kaia starts with.**

| Input Scenario | Feasibility |
| -------------- | ----------- |
| Structured fields (asset_type: "centrifugal_pump") | Easy |
| Semi-structured (spreadsheet with some typed fields) | Medium |
| Free-text ("there's a pump somewhere") | Hard |

## Next Step: Request from Kaia

To validate feasibility, we need ONE worked example:

> "Please provide:
> 1. **Input:** What data do you START with? (screenshot or export from Bruce BEM)
> 2. **Output:** What CCI code did you CREATE?
> 3. **Process:** What steps did you take to look it up?"

This tells us if the input structure supports automation.

## Resources

- [CCI-EE Tables](https://ehituskeskus.ee/cci/ee-tabelid/) (Estonian, Excel downloads)
- [CCI-EE Guidance PDF](https://ehituskeskus.ee/wp-content/uploads/2022/03/CCI-EE-guidance-material_14.02.2022_70-p_En.pdf) (English, 70 pages)
- [FlowBIM CCI Introduction](https://flowbim.ee/cci-ee-context/introduction-cci-ee/)
