---
publish: true
---

# Meeting Agenda: Validation Pairs for Soßen Sourcing POC
[[client-avo]]

## Meeting Goal

Obtain validation data from Herr Behrens to calibrate and test the recipe similarity algorithm.

1. **Evaluation pairs obtained** — 5 known similar recipe pairs with confidence levels and reasoning for anchors
2. **Negative examples identified** — 1–2 pairs that share ingredients but are NOT similar (if available)

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Here's what our algorithm will show you back

We've built a recipe comparison system that, for any recipe, finds the 5 most similar ones and shows why they're similar. Here's an example of the output:

```
Query: R261800

Rank  Recipe    Overlap  Proportion  Category  Combined
#1    R261700   85%      97%         N/A       92%
#2    R482000   62%      78%         N/A       71%
#3    R954700   58%      82%         N/A       68%
#4    R445100   51%      65%         N/A       57%
#5    R887200   48%      71%         N/A       55%
```

- **Overlap:** How many ingredients the two recipes share
- **Proportion:** How close the amounts are for shared ingredients
- **Category:** Whether unshared ingredients belong to the same purchasing group

### 2. To calibrate, we need known recipe pairs from you

To test whether the algorithm finds the right results, we need known similar pairs from your experience. We've prepared a simple table:

| Recipe_A | Most similar (required) | Others (optional) | Confidence | Why? (if "definitely") |
|----------|------------------------|-------------------|------------|------------------------|
| R...     | R...                   | R..., R...        | Definitely / Probably / Unsure | Brief note |

For each pair, we need the recipe IDs and how confident you are. For pairs you're most certain about, a brief note on why helps us understand what "similar" means from a domain perspective.

### 3. A mix of pair types helps us calibrate better

We get the most calibration value from a mix of pairs — not just the most obvious duplicates. If some pairs are very similar and others are more borderline, that range helps us tune the algorithm across the full spectrum.

### 4. Pairs that look similar on paper but actually aren't

If you know of recipe pairs that share many ingredients but you would not consider similar — for example, recipes using the same base sauce for completely different product lines — even 1–2 examples would help us test that the algorithm correctly rejects false matches.

## Meeting Format

- **Type:** Discovery — obtaining validation data
- **Expectation:** Herr Behrens comes with knowledge of which recipes he considers similar
- **Outcome:** Filled evaluation table + optional negative examples

## Related

- **Issue:** [#513: AVO: KI_PROJEKT_SOSSEN_SOURCING POC](https://github.com/DaveX2001/deliverable-tracking/issues/513)
- **Design Doc:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/design-doc-sossen-sourcing-poc)
