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

### Part 1: Data Assessment [TOUCHED]

**What we have:** REKERS delivered test data on 2026-01-28 (14 projects vs 10 agreed). Two CSVs + 14 project folders:
- **Level 1 — Anfragen.csv** (111 rows, 30 cols): Project requests with metadata (name, location, dates, amounts). Denormalized with AnfrageKunde and Angebot tables.
- **Level 2 — Angebote ohne Material.csv** (4,307 rows, 141 cols): Quotation line items with detailed cost breakdowns, component types, labor hours. Not mentioned in transcript — surprise delivery.
- **File folders** (~740 files after ZIP extraction): 482 EML, 210 PDF, 5 DWG, 3 DXF, 2 XLSX. No IFC files.

**What we don't have:**
- **Level 3 — Kommissionen** (actual executed projects): Not delivered. Transcript acknowledges: "Am Anfang haben wir leider nicht die Kommissionsebene."
- **Evaluierungsliste** (expected results table from Herr Sasse): Not delivered. This is needed for testing (→ Part 4 blocker).
- **Kunden details**: Customer IDs present but no customer master data. Transcript ambiguous on whether this was agreed.

**Similarity search operates at Level 1:** Input = new Anfrage → Output = similar historical Anfragen with their Angebote (pricing reference). Transcript evidence: "hier ist die Anfrage, das muss das Ergebnis sein."

**Core feasibility question:** The 9 similarity criteria (see Part 3) are NOT stored as structured fields in either CSV. They must be EXTRACTED from unstructured content (EMLs, PDFs, DWGs) per project. Whether this extraction is feasible = Part 2.

**Detailed inventory:** [Data Assessment Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)

**Transcript:** [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) + [Wed Follow-up](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4)

### Part 2: File Processing Feasibility [UNDEFINED — needs data]

Determine which file formats can be processed for semantic content extraction. PDFs and EMLs are straightforward. IFC models depend on whether they're architecture vs structural (different metadata richness). DWG is binary — needs specialized tooling.

**Transcript:** [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (file format discussion: IFC architecture vs structural, DWG, EML, XLSX)

### Part 3: Similarity Matching Approach [TOUCHED]

Project-level matching: given a new Anfrage (request), find the most similar historical Anfragen from the reference set. The matched Anfragen's Angebote (offers) provide the pricing reference value.

**Verified similarity criteria** (from Wed Follow-up transcript + handwritten notes, confirmed against each other):

1. **Gebäudetyp** — building type (e.g., Hallengebäude, Bürogebäude)
2. **Höhe** — height ("height is decisive for column calculations")
3. **Kran, nicht Kran** — crane or no crane (boolean)
4. **Dachlasten** — roof loads
5. **Bauort mit gleichen Dachlasten** — construction location combined with matching roof loads
6. **Kundenreferenz** — customer reference (complex: same Bauherr may have multiple GUs)
7. **Kalkulatorenwissen** — calculator expertise ("future-oriented" — not currently available)
8. **Baustoff** — building material (precast vs cast-in-place, wood roof structures)
9. **Dachbegrünung, nicht Dachbegrünung** — roof greening or not ("decisive for calculations")

**Rejected:** Photovoltaik ("not really relevant anymore")

**Not in transcript:** Achsraster, Zwischenebene (these were in the previous AI session's interpretation but are NOT verified)

**Ranking approach:** Partial matching — not all criteria will match. AI ranks by how many criteria match (3 of 5 > 1 of 5). Three-tier thresholds (80%/70%/60%), 60% cutoff. Top-K results (configurable, e.g., top 10 or 15).

**Background — abandoned approach:** Workshop 2 initially explored component-level matching (individual beams, columns matched by dimensions + loads). This was abandoned due to complexity of cross-location load normalization (wind, snow, earthquake). Project-level matching pre-filters the search space before any component analysis.

**Transcripts:** [Tue AM](https://app.fireflies.ai/view/01KFZ9MSE9WZ179TAYYC30C6JC) (component-level parameters, yellow fields) + [Tue PM](https://app.fireflies.ai/view/01KFZR74V0N0P1S589KBKZCBYH) (search/return parameters, variance tolerances 3-10%) + [Wed Follow-up](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4) (verified criteria list, ranking approach)

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
