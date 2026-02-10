---
publish: true
---

# Design Doc: Soßen Sourcing POC — Recipe Similarity Algorithm
[[client-avo]]

Design for the recipe similarity POC: compare ~90 test recipes, find Top 5 most similar per query, validate against known duplicate pairs.

---

## Problem Statement

AVO-Werke has ~8000 sauce recipes accumulated over years. Duplicates and near-duplicates are invisible — no system compares recipes systematically. Manual comparison takes 2+ person-days just to display the data. Redundant recipes mean redundant production runs, inventory, and QA.

**Preconditions:**
- ~90 test recipes available for POC development
- Three comparison criteria agreed: ingredient overlap, percentage similarity, category matching
- Category matching and certification filtering require additional data not yet delivered
- Process-based similarity (manufacturing timing) is out of scope — deferred by agreement

**NOT the problem:**
- Production optimization / batch sizing (post-POC)
- Preventive duplicate detection from product requests (post-POC)
- Process-based similarity (deferred — data not in SAP, agreed in WS2-Session3a + Jan 15 meeting)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | For ~90 test recipes: query any recipe → receive Top 5 most similar recipes with similarity scores |
| **Success** | (1) Behrens's 5 known duplicate pairs each appear in their partner's Top 5 results. (2) Three criteria (overlap ratio, percentage similarity, category match) scored per pair — weights calibrated from known pairs. (3) Output: ranked list per query (recipe ID + score). Ingredient drill-down is next iteration. |
| **Done test** | Query each recipe from Behrens's 5 known pairs → partner appears in Top 5. If yes for all 5 → baseline validated. |

**Blocked by:** 5 known duplicate pairs from Herr Behrens (meeting agenda item).

---

## Approach

### Part 1: Data Loading — Deepened

Load `alle.jsonl` → filter to **R-prefix entries only** (62 final products) → each recipe becomes a map: `{material_id: percentage}`. Materials are already exploded (flattened across all Vormischung levels), summing to ~100%.

**Input:** `alle.jsonl` (197 total entries, now in `data/` directory)

**Output:** 62 recipe maps (`R`-prefix), each `{material_id: percentage}` — this is the frame of reference for all similarity scoring.

**Entry type breakdown (from data analysis):**

| Prefix | Count | Used in POC? |
|--------|-------|-------------|
| R | 62 | Yes — final products |
| V | 54 | No — Vormischungen (pre-mixes, Phase 2) |
| RV | 18 | Unknown — verify with Mattis |
| RZ | 9 | Unknown — verify with Mattis |
| (none) | 47 | No — raw materials / intermediates |
| B | 6 | Unknown — B-variants |

**What we ignore:** The `structure` field (Vormischung traceability — deferred to Phase 2 per WS2-Session1 agreement).

**Basisvariante filter:** WS2-Session3a agreed only Variant 0 (base recipe) enters comparison, filtering ~80% packaging/customer duplicates. May already be applied in test dataset (197 vs 8000 total).

**Open uncertainties (verify with Mattis or transcripts):**
- What are RV, RZ, and no-prefix entries? Some may be final products.
- Is Basisvariante filtering already applied in `alle.jsonl`?

**Source:**
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (44:47) — "Auflösung der Rezepturen auf Zutatsebene ohne Vormischungen" (resolve to ingredient level without premixes)
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (25:26) — "Vormischungen sollten separat verglichen werden" (premixes compared separately = Phase 2)
- [WS2-Session3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) — Basisvariante (Variant 0) only, filters ~80% duplicates
- [Jan 29 transcript](https://app.fireflies.ai/view/01KG3DKZHD0EVFK4620C86B873) — Mattis confirms exploded materials structure, summing to 1.0
- Data analysis (2026-02-09): 197 entries, 62 R-prefix, 54 V-prefix, 134 unique raw materials

### Part 2: Similarity Scoring — Deepened

For each recipe pair, compute three independent scores. Each handles a different aspect of "similar":

**Criterion 1 — Overlap (Jaccard similarity):** "Do they share the same ingredients?"

Binary set comparison: `|A ∩ B| / |A ∪ B|` where A and B are sets of material IDs. Score 0–1, where 1.0 = identical ingredient lists. Client-facing label: **Overlap**.

**Criterion 2 — Proportion (Bray-Curtis similarity):** "For shared ingredients, how close are the amounts?"

For shared materials, measure proportional distance: similarity = `1 - sum|a_i - b_i| / sum(a_i + b_i)`. Score 0–1, where 1.0 = identical proportions. Naturally weights high-percentage ingredients more than trace ingredients. Client-facing label: **Proportion**.

**Criterion 3 — Category (Einkaufsgruppen matching):** "For UN-shared ingredients, are they substitutable?"

For materials NOT in the intersection, check if they belong to the same Rohstoffkategorie (761 Einkaufsgruppen). Ratio of categorically-matched unshared materials to total unshared. Client-facing label: **Category**. Blocked on Stammdaten CSV from Mattis; data quality currently poor.

**Combined weighting:** Empirical — start equal weights (33/33/33), let Behrens's validation pairs reveal which criterion matters most. Transcripts emphasize "quantity-based similarity" (WS2-S3a), suggesting Proportion may ultimately dominate — hypothesis to test, not pre-commit.

**Design context:** Algorithmic approach chosen over vector/embedding because similarity is quantity-based, not semantic — client needs explainable, per-criterion scores, not opaque numbers.

**Source:**
- [Pflichtenheft §3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing) — FR-02 (Material_ID matching), FR-02a (overlap ratio), FR-04 (percentage similarity), FR-08 (category matching)
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (49:53) — "Ähnlichkeitsschwellen flexibel: 2%, 5%, 10%", 3% fluctuation acceptable
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (12:45) — "nicht nur auf Namen, sondern auf die tatsächliche Zusammensetzung" (actual composition, not names)
- [WS2-Session3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) — algorithmic > vector ("keine semantische, sondern mengenbasierte Ähnlichkeit"), 761 Einkaufsgruppen, data quality poor
- [WS2-Session3b](https://docs.google.com/document/d/1NhjxZnugaiWLg415HbC7nB_ybDPOCaCbfM0zF3L_n0I) — 98% similarity target, user-adjustable tolerance sliders

### Part 3: Ranking + Output — Deepened

Rank all 62 R-prefix recipes by combined similarity score for a given query. Return **Top 5** (configurable N) with per-criterion scores using client-facing labels.

**POC output format** (plain scores — ingredient drill-down is next iteration):

```
Query: R261800

Rank  Recipe    Overlap  Proportion  Category  Combined
#1    R261700   85%      97%         N/A       92%
#2    R482000   62%      78%         N/A       71%
#3    R954700   58%      82%         N/A       68%
#4    R445100   51%      65%         N/A       57%
#5    R887200   48%      71%         N/A       55%
```

**Client-facing labels:** Overlap, Proportion, Category, Combined — no algorithm names (Jaccard, Bray-Curtis) exposed to client.

**Top N:** Default 5, configurable. Workshop discussed "five to ten" (WS2-S3a), but Top 5 is the stated POC criterion.

**Decided:** Plain scores for POC. Full vision (post-POC): Übersicht-style Excel with ingredients, quantity differences, percentage matches, and color-coded deviation markers per WS2-S3b. The POC validates the algorithm; the Excel output is a presentation layer added after validation passes.

**Source:**
- [Pflichtenheft §9](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing) — Top 5 output, three criteria separately
- [WS2-Session3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) — "fünf bis zehn ähnlichsten Rezepturen" (five to ten)
- [WS2-Session3b](https://docs.google.com/document/d/1NhjxZnugaiWLg415HbC7nB_ybDPOCaCbfM0zF3L_n0I) — Excel output with color-coded deviations (full vision)
- Übersicht.xlsx — AVO's existing recipe comparison format (from AVO Seafile)

### Part 4: Validation — Deepened (Blocked on Behrens pairs)

**POC scope:** Validate the algorithm against Behrens's 5 known pairs. This is algorithm validation ("does it find expected pairs?"), not discovery ("find new duplicates"). New discoveries during POC are observed but not formally validated — discovery becomes the production goal.

**Similarity type:** Near-duplicates with flexible thresholds (2%, 5%, 10% per WS2-Session1), not exact matches. Quantity-based, not semantic. The specific similarity band that matters will emerge empirically from the data and Behrens's pairs.

**Evaluation table (Behrens fills in):**

| Recipe_A | K1 (required) | K2–K5 (optional) | Confidence | Why? (if "definitely") |
|----------|---------------|-------------------|------------|------------------------|
| R...     | R...          | R..., R...        | Definitely / Probably / Unsure | Free-text domain reasoning |

- **K1:** Primary similar recipe (required). K2–K5 optional if Behrens knows additional similar recipes.
- **Confidence:** Quick checkbox — low burden. "Definitely" pairs are calibration anchors; "probably/unsure" are bonus exploration data.
- **Why?:** Free-text reasoning only for "definitely" pairs. Enables calibration conversation when algorithm disagrees with domain expert.

**Guided pair selection:** Request a diverse mix — 2 obvious duplicates, 2 similar-but-different, 1 edge case. Diverse pairs maximize informative value for weight calibration (homogeneous pairs all score high on the same criteria, revealing nothing about relative weights).

**Negative examples (if possible):** Ask Behrens for 1–2 pairs that share many ingredients but are NOT similar. These test false positive rejection. If not provided, the 62-recipe dataset (1,891 possible pairs) provides implicit negatives.

**Success criterion:** K1 partner appears in query recipe's Top 5 (configurable N). Test bidirectionally — query A→check B in Top 5 AND query B→check A in Top 5 — as sanity check (all three criteria are mathematically symmetric, so results should match).

**Calibration workflow:** Run algorithm with equal weights → compare output to Behrens's table → for "definitely" pairs where algorithm disagrees, use the "why?" reasoning to understand which criterion the domain expert values → adjust weights empirically. This is iterative, not one-shot.

**Blocked by:** 5 known duplicate pairs from Herr Behrens (meeting agenda item).

**Source:**
- [Pflichtenheft §9](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing) — evaluation methodology, success criteria
- [Nov 8 transcript](https://app.fireflies.ai/view/01K9J1PK1YHP4HXC5VD0NJGWT2) — 3→5 pairs negotiation (Thomas pushed for 5, customer compliance concern)
- [Jan 29 transcript](https://app.fireflies.ai/view/01KG3DKZHD0EVFK4620C86B873) — evaluation table action item for Behrens
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (49:53) — flexible thresholds 2%, 5%, 10%
- [WS2-Session3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) — "automatisierte Qualitätskontrollen finden Dubletten" (system expected to discover unknown duplicates)
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (54:09) — "lebendes System" with user feedback loop

---

## Source

- **Transcripts:** [WS2-S1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs), [WS2-S3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw), [WS2-S3b](https://docs.google.com/document/d/1NhjxZnugaiWLg415HbC7nB_ybDPOCaCbfM0zF3L_n0I), [Jan 15 Fireflies](https://app.fireflies.ai/view/01KEXRBAF5495NKTGZ5N6FHFVE), [Jan 29 Fireflies](https://app.fireflies.ai/view/01KG3DKZHD0EVFK4620C86B873)
- **Pflichtenheft:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing)
- **Agreed Schema:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/agreed-json-schema)
- **Data:** `alle.jsonl` (62 R-prefix final products, 197 total entries, 134 unique materials)
- **Output Template:** Übersicht.xlsx (AVO Seafile)
- **Certification Criteria:** Email from Johannes von Schultz (Oct 30, 2025) — VLOG, Bio, Halal, Kosher, Vegan, Vegetarisch
- **Rohstoffkriterien:** Rohstoffkriterien zur Vergleichbarkeit.pdf (AVO Seafile)
- **Session (Part 1 deepening):** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-AVO--poc/451ac158-4b2b-4501-8dca-bd9645888e7d.jsonl`
- **Session (Parts 2–4 deepening):** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-AVO--poc/ed3ed7d2-a582-4987-b15b-d59a0f7763de.jsonl`
