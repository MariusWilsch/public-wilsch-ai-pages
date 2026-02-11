---
publish: true
---

# Meeting Agenda: Data Confirmation for Soßen Sourcing POC
[[client-avo]]

## Meeting Goal

By the end of this session, we should have:

1. **Data scope confirmed** — which entries from alle.jsonl are final products for comparison
2. **Material treatment agreed** — how ingredient variants (e.g., 00004 vs 00004B) behave in the similarity comparison
3. **Data preparation clarity** — whether Basisvariante filtering was applied to the test data

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The matching pool contains 62 R-prefix entries

From alle.jsonl (197 entries), 62 have R-prefix (final products), 18 RV (premix recipes), 9 RZ (intermediate mixes), 6 B-prefix (unclassified).

We want to confirm:
- Are these 62 the complete set of final products in the test data?
- What are B-prefix entries — should any be in the comparison pool?

### 2. Basisvariante filtering in the test data

The design agreed only Variant 0 (base recipe) enters comparison. Our analysis found no variant duplicates among the 62 R-entries.

We want to confirm:
- Was Variant 0 filtering applied when preparing alle.jsonl?

### 3. Materials with suffix variants

00004 and 00004B appear as separate material IDs. The POC treats them as distinct ingredients — they don't match each other in the similarity comparison.

We want to understand:
- Is this the correct treatment?
- Are suffix variants always the same base substance in different quality grades?

### 4. Stammdaten CSV for future category comparison

Category-level grouping (Einkaufsgruppen) is not needed for POC launch but would enable testing whether recognizing substitutable ingredients improves results.

- When can the Stammdaten CSV be delivered?

---

## Meeting Format

- **Type:** Confirmation session
- **Expectation:** Mattis reviews data preparation decisions
- **Outcome:** Green light on data assumptions for POC implementation

---

## Related

- **Issue:** [#513 - AVO: KI_PROJEKT_SOSSEN_SOURCING POC](https://github.com/DaveX2001/deliverable-tracking/issues/513)
- **Design Doc:** [Soßen Sourcing POC](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/design-doc-sossen-sourcing-poc)
