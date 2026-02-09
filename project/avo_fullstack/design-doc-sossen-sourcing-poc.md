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

### Part 1: Data Loading — Touched

Load `alle.jsonl` → each recipe becomes a map: `{material_id: percentage}`. All entries have exploded materials summing to 100%.

**Uncertainty:** Compare all 197 entries or filter to ~90 LEAF (final) recipes only. Resolve during validation.

**Source:**
- [Jan 29 transcript](https://app.fireflies.ai/view/01KG3DKZHD0EVFK4620C86B873) — Mattis confirms structure, materials summing to 1.0
- Data analysis: 197 entries, 90 LEAFs, 134 unique raw materials, median 7 materials per recipe

### Part 2: Similarity Scoring — Touched

For each recipe pair, compute three scores:

**Overlap ratio:** |shared materials| / |union of materials|. Understood conceptually but exact formula not deep-dived.

**Percentage similarity:** For shared materials, quantify how close the amounts are. Multiple possible formulas — undefined until reverse-engineered from known pairs.

**Category match:** Match unmatched ingredients by Rohstoffkategorie (761 Einkaufsgruppen). Blocked until Stammdaten CSV from Mattis.

**Combined weighting:** How to combine three scores into one ranking. Calibrate from known pairs.

**Source:**
- [Pflichtenheft §3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing) — FR-02 (Material_ID matching), FR-02a (overlap ratio), FR-04 (percentage similarity), FR-08 (category matching)
- WS2-Session1 — "Ähnlichkeitsschwellen flexibel: 2%, 5%, 10%", 3% fluctuation acceptable
- [WS2-Session3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) — algorithmic > vector approach, 761 Einkaufsgruppen
- [WS2-Session3b](https://docs.google.com/document/d/1NhjxZnugaiWLg415HbC7nB_ybDPOCaCbfM0zF3L_n0I) — 98% similarity target

### Part 3: Ranking + Output — Touched

Rank all recipes by combined similarity score for a given query. Return Top 5 with per-criterion scores.

Example output:
```
Query: R261800
  #1  R261700  score: 0.96  (overlap: 0.85, pct_sim: 0.97, cat: N/A)
  #2  R482000  score: 0.82  (overlap: 0.62, pct_sim: 0.78, cat: N/A)
  #3  R954700  score: 0.71  ...
```

**Uncertainty:** Plain scores list vs Übersicht-style Excel (side-by-side ingredients). Plain scores first, ingredient drill-down next iteration.

**Source:**
- [Pflichtenheft §9](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing) — Top 5 output, three criteria separately
- [WS2-Session3b](https://docs.google.com/document/d/1NhjxZnugaiWLg415HbC7nB_ybDPOCaCbfM0zF3L_n0I) — Excel output with color-coded deviations (full vision)
- Übersicht.xlsx — AVO's existing recipe comparison format (from AVO Seafile)

### Part 4: Validation — Touched (Blocked)

For each of Behrens's 5 known pairs: query recipe A → check if B appears in Top 5 (and vice versa). Analyze which criterion drives the match → adjust weights.

**Blocked by:** 5 known duplicate pairs from Herr Behrens.

**Source:**
- [Pflichtenheft §9](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing) — evaluation methodology, success criteria
- Nov 8 transcript — 3→5 pairs negotiation (Thomas pushed for 5)
- [Jan 29 transcript](https://app.fireflies.ai/view/01KG3DKZHD0EVFK4620C86B873) — evaluation table action item for Behrens

---

## Uncertainties → Meeting Agenda

| # | Uncertainty | Who Resolves | Ask |
|---|------------|-------------|-----|
| 1 | **5 known duplicate pairs** | Herr Behrens | "From the test dataset, identify 5 pairs you know are duplicates/similar. Which of the three criteria matters most?" |
| 2 | **Stammdaten CSV** | Mattis | "When can we expect the CSV with Rohstoffkategorie + certifications (VLOG, Bio, Halal, Kosher, Vegan, Vegetarisch) per material?" |
| 3 | **Percentage similarity formula** | Self (after receiving pairs) | Reverse-engineer from known pairs |
| 4 | **Combined score weighting** | Self (after receiving pairs) | Calibrate from known pairs |

---

## Source

- **Transcripts:** [WS2-S1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs), [WS2-S3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw), [WS2-S3b](https://docs.google.com/document/d/1NhjxZnugaiWLg415HbC7nB_ybDPOCaCbfM0zF3L_n0I), [Jan 15 Fireflies](https://app.fireflies.ai/view/01KEXRBAF5495NKTGZ5N6FHFVE), [Jan 29 Fireflies](https://app.fireflies.ai/view/01KG3DKZHD0EVFK4620C86B873)
- **Pflichtenheft:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing)
- **Agreed Schema:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/agreed-json-schema)
- **Data:** `alle.jsonl` (90 LEAF recipes, 197 total entries, 134 unique materials)
- **Output Template:** Übersicht.xlsx (AVO Seafile)
- **Certification Criteria:** Email from Johannes von Schultz (Oct 30, 2025) — VLOG, Bio, Halal, Kosher, Vegan, Vegetarisch
- **Rohstoffkriterien:** Rohstoffkriterien zur Vergleichbarkeit.pdf (AVO Seafile)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-AVO--poc/451ac158-4b2b-4501-8dca-bd9645888e7d.jsonl`
