---
publish: true
---

# REKERS KI-gestütztes Angebotssystem — Feasibility Report Design Doc
[[client-rekers]]

V006 deliverable: feasibility assessment with Go/No-Go recommendation and POC implementation plan for AI-powered project-level similarity matching over 30 years of REKERS engineering history.

---

## Problem Statement

REKERS Beton GmbH has 30 years of engineering project history distributed across individuals and disparate data systems (IBM i database, Linux file systems). When a new quotation request arrives, finding relevant historical reference projects requires extensive personal knowledge and manual search — a process that depends on a few key personnel and takes days per request.

The problem is NOT building a full AI quotation system. The problem IS determining whether AI-powered project-level similarity matching is feasible given REKERS' actual data, and defining what a Proof of Concept would look like.

**Preconditions:**

- Workshop 2 completed (Jan 27-28, 2026) — scope and approach defined
- REKERS committed to delivering test data (10 projects as CSV + file folders)
- Project-level matching adopted over component-level matching (complexity reduction)
- V006 contract covers this feasibility assessment (1.5 days analysis work)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Deliver V006 feasibility report with honest Go/No-Go recommendation and POC plan |
| **Success** | Report answers: (1) Is the data quality sufficient? (2) Can project-level matching work? (3) What would a POC cost and involve? Optional: demonstrate feasibility with indexed/chunked sample data |
| **Done test** | "Can I write a meeting agenda with open design questions?" → If NO → design is complete |

---

## Approach

### Part 1: Data Assessment [UNDEFINED — needs data]

Evaluate the 10 test projects REKERS delivers: CSV table structure (Anfragen, Kunden, Protokolle), file folder contents (PDF, EML, IFC, DWG, XLSX), data quality, completeness, and format distribution.

**Transcript:** [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (data export structure, CSV format, folder organization defined here)

### Part 2: File Processing Feasibility [UNDEFINED — needs data]

Determine which file formats can be processed for semantic content extraction. PDFs and EMLs are straightforward. IFC models depend on whether they're architecture vs structural (different metadata richness). DWG is binary — needs specialized tooling.

**Transcript:** [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (file format discussion: IFC architecture vs structural, DWG, EML, XLSX)

### Part 3: Similarity Matching Approach [DEFINED]

Project-level matching: given a new request, find the most similar historical project from the reference set. Similarity criteria from Workshop 2: Gebäudetyp, Höhe, Kran, Dachlasten, Achsraster, Dachtragwerk Material, Bauort, Zwischenebene. Ranking with 3-10% variance tolerance.

**Background — abandoned approach:** Workshop 2 initially explored component-level matching (individual beams, columns matched by dimensions + loads). This was abandoned due to complexity of cross-location load normalization (wind, snow, earthquake). Project-level matching pre-filters the search space before any component analysis.

**Transcripts:** [Tue AM](https://app.fireflies.ai/view/01KFZ9MSE9WZ179TAYYC30C6JC) (component-level parameters, yellow fields) + [Tue PM](https://app.fireflies.ai/view/01KFZR74V0N0P1S589KBKZCBYH) (search/return parameters, variance tolerances 3-10%)

### Part 4: Validation Design [UNDEFINED — discussed but not designed]

Direction from Workshop 2 follow-up: known-answer test with ranking thresholds (80%/70%/60%), red herring projects, parameter weighting by component type. Needs formal design before POC execution.

**Transcript:** [Wed Follow-up — Priorisierung & UI](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4) (ranking thresholds, parameter weighting, test case design)

### Part 5: Infrastructure Assessment [UNDEFINED — secondary priority]

Target: IBM Power 10 (~20 TOPS per chip). Alternative: hybrid approach (external GPU training, local Power 10 inference). Only relevant after Go/No-Go decision — does not affect feasibility assessment itself.

**Transcripts:** [Wed AM](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (Power 10 performance, ~20 TOPS) + [Tue AM](https://app.fireflies.ai/view/01KFZ9MSE9WZ179TAYYC30C6JC) (hybrid GPU/Power approach)

### Part 6: POC Scope Definition [PARTIALLY DEFINED]

**Known:** 10 historical projects, project-level matching only. Component-level matching deferred. Batch processing model (overnight AI, morning review).

**Unknown:** Contract terms, effort estimate, timeline — derived from feasibility assessment results. Next step after Go: create POC contract based on findings.

**Transcript:** [Wed AM](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (10-project scope, project-level first, batch processing model)

---

## Source

**Transcripts:**

- [Tue AM — Kalkulation & Metadatenextraktion](https://app.fireflies.ai/view/01KFZ9MSE9WZ179TAYYC30C6JC) (abandoned component-level approach)
- [Tue PM — Lastmodellierung & Suchparameter](https://app.fireflies.ai/view/01KFZR74V0N0P1S589KBKZCBYH) (pivot discussion)
- [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (adopted project-level approach — most critical)
- [Wed Follow-up — Priorisierung & UI](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4) (validation design direction)

**Artifacts:**

- [V006 Offer (contract basis)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/v006-offer-wph)
- [KBB Workshop 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/KBB_2026_01_27-28_Workshop2)
- [Gelbe Felder Definition](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/gelbe-felder-definition)

**Issue:** [#629 — REKERS: KI-gestütztes Angebotssystem - Workshop 2](https://github.com/DaveX2001/deliverable-tracking/issues/629)

**Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects/fc9f193e-2d54-4ea9-bcc1-5dfd2dd35f2d.jsonl`
