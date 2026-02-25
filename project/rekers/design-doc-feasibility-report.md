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
- REKERS committed to delivering test data (10 projects as CSV + file folders) — 14 delivered
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

### Part 1: Data Assessment — Does the data exist?

**Three-level model** (our synthesis, based on REKERS database entities Anfragen/Angebote/Kommissionen):

| Level | Entity | Rule | Data Available |
|-------|--------|------|----------------|
| **Level 1** | Anfragen (requests) | Customer's world — what they want | CSV row + files (EMLs, PDFs, plans) |
| **Level 2** | Angebote (offers) | REKERS's response — how they price it | Detailed cost breakdowns, component specs |
| **Level 3** | Kommissionen (commissions) | Execution — what was built | NOT delivered — deferred to POC (live system access required) |

**What we have:** REKERS delivered test data on 2026-01-28 (14 projects, all historical). All files live in the same folder per project — levels are logical, physical storage is mixed.

*Example: Anfrage 35764 — Neubau Werkhalle van Eckendonk, Gescher (including but not limited to):*

**Level 1 — Customer input** (what arrives):
```
35764/10593/  Angebotsaufforderung Stahlbau.eml     ← architect's initial request
35764/11239/  van Eckendonk_Planunterlagen.eml       ← plans from architect
              van Eckendonk_Projektunterlagen.eml    ← project specs
              van Eckendonk_Stützen.eml              ← column specifications
              Baugenehmigung.pdf                     ← building permit (external)
              Bodengutachten Schleicher.pdf           ← soil survey (external)
              BA-1000d_Grundrisse+Schnitt.dwg        ← floor plans (architect)
```

**Level 2 — REKERS response** (what REKERS creates):
```
35764/11239/  Angebot Nr. 28273.20.pdf               ← quotation document
              Angebot Nr. 28273.30.pdf               ← quotation document
              BV. Neubau Werkhalle van Eckendonk.eml ← REKERS internal emails
```

**Angebote ohne Material.csv** (Level 2 structured data, 4,307 rows, 141 cols): Quotation line items with cost breakdowns per component — weights, crane costs, material costs, crew sizes. Surprise delivery (not discussed in transcript). Assessed: contains no criteria-relevant data beyond Anfragen.csv (KRANKOSTEN = universal installation crane cost for precast, not building crane; material weights = REKERS product, not building material type). → Meeting agenda: why was this included?

**Level 3 — Execution** (what was built): Not delivered — deferred to POC.

**Across all 14 projects:** Dates 2019–2025, €1.2M–€15.3M. File inventory has two layers:

| Layer | Total | Key types |
|-------|-------|-----------|
| **L0: Visible** (folder files) | 565 | 504 EML, 44 PDF, 17 ZIP |
| **L2: Hidden** (EML attachments) | 1,322 | 660 PDF, 26 XLSX, 12 DWG, 435 images |

The ~740 files previously reported counted L0 + pre-extracted ZIPs. EML attachments (L2) were uncounted — yet contain the majority of criteria-rich PDFs. Every project is 2.2x–3.2x richer than the file system shows. ZIPs are containers at any level (in folders or inside EML attachments) — extract wherever found, then all rules apply to contents.

**Folder structure** (Anfrage ID = project, subfolders = customer links, verified 1:1 match with CSV):

```
REKERS Test Data/
├── Anfragen.csv                (14 Anfragen × AnfrageKunde × Angebot = 111 rows)
├── Angebote ohne Material.csv  (4,307 quotation line items)
├── Wilsch.zip                  (original delivery, 2.1GB)
│
├── 35764/ — Neubau Werkhalle van Eckendonk (2019, €491K)
│   ├── 10593/ (2 files)
│   └── 11239/ (23 files)
├── 36185/ — Lagergebäude GGM Gastro (2019, €3.9M)
│   ├── 11167/ (34 files)
│   ├── 13169/ (2 files)
│   ├── 15846/ (7 files)
│   └── 19866/ (0 files)
├── 37369/ — Kühl- und Lagerhalle Schütte (2020, €2.0M)
│   └── 12687/ (19 files)
├── 38043/ — Neubau Nokera Werk Stegelitz (2021, €15.3M)
│   └── 13533/ (91 files)
├── 38882/ — VGP Park Erfurt 3 (2022, €3.5M)
│   ├── 14578/ (25 files)
│   └── 15026/ (15 files)
├── 39381/ — VGP Park Magdeburg Halle D (2022, €11.8M)
│   └── 15214/ (48 files)
├── 40138/ — Hillwood (2023, €1.7M)
│   └── 16182/ (29 files)
├── 40593/ — Neubau Logistikzentrum (2023, €6.1M)
│   └── 16743/ (35 files)
├── 40758/ — Pfenning Neubau Halle (2024, €4.7M)
│   ├── 16946/ (51 files)
│   ├── 17013/ (3 files)
│   ├── 17146/ (3 files)
│   └── 18555/ (0 files)
├── 40856/ — Neubau Logistikhalle (2024, €1.8M)
│   └── 17085/ (17 files)
├── 41634/ — VGP Park Leipzig Halle C (2024, €5.6M)
│   └── 18080/ (22 files)
├── 41740/ — VGP Park Bernau Halle B (2024, €2.3M)
│   └── 18213/ (24 files)
├── 41793/ — Lager- und Logistikhalle Roy Böhlke (2025, €2.1M)
│   └── 18282/ (18 files)
└── 41807/ — NB Logistikhalle Spedition Neukirchen (2025, €1.3M)
    └── 18297/ (18 files)
```

**What we don't have:**

- **Level 3 — Kommissionen**: Deferred to POC — extraction from IBM i is complex, requires live system access.
- **Evaluierungsliste** from Herr Sasse: Not delivered (→ Part 4 blocker).
- **New request examples**: All 14 projects are historical. No example of what a new request looks like at arrival time — the trigger/input for the matching system is undefined (→ meeting agenda).

**Observation — absent ≠ no:** For criteria extracted from files, absence of information doesn't mean the criterion value is "no" — it means "undocumented." This applies to all extraction criteria. Whether any criteria are must-haves vs nice-to-haves requires both client input (meeting agenda) and empirical testing (POC).

**Detailed inventory:** [Data Assessment Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)

**Transcript:** [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) + [Wed Follow-up](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4)

### Part 2: File Processing Feasibility — Can we extract it?

Prerequisite for Part 3: 6 of 9 matching criteria require information extraction from project files (see Part 3 criteria mapping).

**File types and extraction approach:**

| Type | L0 | L2 (in EMLs) | How to read | Notes |
|------|----|-------------|-------------|-------|
| EML | 504 | — | Body: text parsing. Attachments: extract to disk first. | Containers — not files. 660 PDFs + 26 XLSX + 12 DWG hidden inside. |
| PDF | 44 | 660 | Read tool (renders as images natively). NOT text extraction. | 5x more criteria found via visual read vs text parse. |
| ZIP | 17 | 8 | Extract wherever found (on disk or inside EMLs), then apply all rules to contents. | Pre-extracted to `_extracted/` dirs for L0 ZIPs. |
| XLSX | 2 | 26 | openpyxl via bash. | Includes Kranliste.xlsx (crane specs), Abfallbilanz.xlsx (DGNB). |
| DWG | 5 | 12 | ❌ Out of scope — binary CAD, needs specialized tooling. | |
| DXF | 3 | — | ⚠️ Out of scope — parseable but complex. | |

**All file types are equal criteria sources** — no hierarchy. Extraction technique varies by type, but email body text, XLSX cells, and PDF pages all contribute to criteria discovery equally.

**Criteria extraction evidence** (all 14 projects — complete findings):

| Project | L0 | L2 | Extracted | Notes |
|---------|----|----|-----------|-------|
| [35764 — Werkhalle van Eckendonk](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-35764) | 25 | 65 | 5/6 | Kran 2×ABUS 40t/10t; Dachbegrünung ❌ |
| [36185 — Lagergebäude GGM Gastro](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-36185) | 67 | 217 | 4/6 | Kran ❌, Dachbegrünung ❌ |
| [37369 — Kühl/Lagerhalle Schütte](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-37369) | 19 | 74 | 4/6 | 40m Satteldachbinder; Kran ❌, Dachbegrünung ❌ |
| [38043 — Nokera Werk](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-38043) | 104 | 556 | 5/6 | Fertigteilwerk; Kranbahnrahmen 3/4 Hallen; Dachbegrünung ❌ |
| [38882 — VGP Park Erfurt 3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-38882) | 42 | 47 | 4/6 | Kran ❌; Dachbegrünung explizit ausgeschlossen |
| [39381 — VGP Park Magdeburg D](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-39381) | 50 | 96 | 5/6 | 20t Kran Sonderbereich; Dachbegrünung ❌ |
| [40138 — Hillwood](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-40138) | 6 | 80 | 3/6 | Holz-Fischbauchbinder; Kran ❌, Dachlasten ❌, Dachbegrünung ❌ |
| [40593 — Logistikzentrum](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-40593) | 39 | 91 | 3/6 | 4 Hallen + Mezzanine; Kran ❌, Dachlasten ❌, Dachbegrünung ❌ |
| [40758 — Pfenning Neubau Halle](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-40758) | 69 | 282 | 5/6 | DGNB Gold; Gründach 1,200 kN/m²; Kran ❌ |
| [40856 — Neubau Logistikhalle](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-40856) | 19 | 63 | 4/6 | Kran ❌, Dachbegrünung ❌ |
| [41634 — VGP Park Leipzig Halle C](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-41634) | 23 | 142 | 4/6 | Kran ❌; Dachbegrünung explizit ausgeschlossen |
| [41740 — VGP Park Bernau B](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-41740) | 24 | ~152 | **6/6** | Einziges 9/9 Projekt; 2×10t Kran + Dachbegrünung 1,00 kN/m² |
| [41793 — Lager/Logistikhalle Böhlke](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-41793) | 27 | 75 | 5/6 | 2×20t Koppelkran; Dachbegrünung explizit ausgeschlossen |
| [41807 — NB Logistikhalle Neukirchen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-41807) | 23 | 48 | 4/6 | F90 Brandschutz; Kran ❌, Dachbegrünung ❌ |

L0 = visible folder files, L2 = extracted EML attachments. Average multiplier: 4.7× (range 3.0–18.5×). Gebäudetyp, Höhe, Baustoff found in all 14 projects. Dachlasten found in 12/14. Kran documented in 5/14. Dachbegrünung documented in 2/14 (explicit exclusion recorded in 3 more).

**Key pattern:** EMLs are containers, not files. The 504 EMLs hold 1,322 attachments (660 PDFs, 26 XLSX, 12 DWG). 4/14 projects have zero standalone PDFs — all criteria-rich data is inside EML attachments. Reading PDFs visually (image rendering) yields 5x more criteria than text extraction. This pattern is structural to how REKERS stores project data.

**Key observation — L1/L2 source split:** Contrary to initial assessment, criteria are split roughly 50/50 between Level 1 (customer/architect documents: Gebäudetyp, Höhe) and Level 2 (REKERS quotations: Dachlasten, Baustoff). Not "mostly Level 2." 3 CSV criteria (Bauort, Kundenreferenz, Kalkulatorenwissen) are 2 L1 + 1 L2. Implications for new request matching depend on what data arrives at request time (→ meeting agenda, Thomas → Wittag).

**Transcripts:**
- [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (file format discussion, data source for criteria)
- [Thomas-Marius Sync 2026-02-11](https://drive.google.com/file/d/11Ttr1zcWoVSZccolGwJkCsWLZ2A-3G2g/view) (Part 2 is core deliverable, criteria extraction evidence for all 14 projects, deadline alignment)

### Part 3: Similarity Matching Approach — Can we match it?

Project-level matching: given a new Anfrage (Level 1 only), find the most similar historical **projects** from the reference set. Historical projects have Level 1 + Level 2 + files — all available data contributes to similarity. The matched projects' Angebote (Level 2) provide the pricing reference value.

**Asymmetry constraint:** New requests have limited data (Level 1 only). Historical projects have full lifecycle data. Matching must handle sparse input against rich references. **What a new request contains at arrival time is undefined** — this is a meeting agenda item (→ Part 4, → meeting agenda).

**Three modes of acquiring criteria:**

| Mode | Criteria | How |
|------|----------|-----|
| **Structured** (CSV) | Bauort, Kundenreferenz, Kalkulatorenwissen | Direct column comparison — always available |
| **Extracted** (documents) | Gebäudetyp, Höhe, Kran, Baustoff, Dachbegrünung | Text mining PDFs/EMLs — inconsistent availability |
| **Derived** (norms) | Dachlasten | Location → wind/snow load norms (DIN EN 1991) |

**Criteria-to-data mapping** (verified against actual CSV columns):

| Criterion | Source | Always available? |
|-----------|--------|-------------------|
| Bauort | CSV: `BAUVORHABEN_ORT, PLZ, NATION` | ✅ Yes |
| Kundenreferenz | CSV: `KUNDE` column | ✅ Yes |
| Kalkulatorenwissen | CSV: `KALKULATOR` column (ID only — who, not what they know) | ⚠️ ID available, knowledge requires extraction |
| Gebäudetyp | File extraction (PDFs, EMLs) | ⚠️ Requires extraction |
| Höhe | File extraction (specs, cross-sections) | ⚠️ Requires extraction |
| Kran | File extraction + Kranliste.xlsx | ⚠️ Requires extraction |
| Dachlasten | File extraction + derivable from location norms | ⚠️ Requires extraction |
| Baustoff | File extraction (specifications) | ⚠️ Requires extraction |
| Dachbegrünung | File extraction | ⚠️ Requires extraction |

3 criteria guaranteed in CSV. 6 require document extraction (see Part 2 for extraction evidence).

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

**Dynamic weighting** [later]: "Wenn ich kalkuliere, ist die Dachbegrünung maßgeblich, wenn ich die Stütze kalkuliere, ist ja Höhe maßgeblich." Explicitly discussed — first prove matching works at all, then optimize weighting. Could be added later via chat interface.

**Ranking approach:** Partial matching — not all criteria will match. AI ranks by how many criteria match (3 of 5 > 1 of 5). Three-tier thresholds (80%/70%/60%), 60% cutoff. Top-K results (configurable, e.g., top 10 or 15).

**Three business outcomes from matching:**
- **High match (~100%):** "Angebot morgen bis 10 Uhr" — quote by tomorrow morning
- **Partial match:** Baukastensystem — combine parts from different reference projects
- **No match:** "Komplett neu entwickeln, dauert vier Wochen" — build from scratch

**UNDEFINED — similarity computation approach:** How to combine structured + extracted + derived criteria into one similarity score. Needs deep dive (→ next session).

**Background — abandoned approach:** Workshop 2 initially explored component-level matching (individual beams, columns matched by dimensions + loads). This was abandoned due to complexity of cross-location load normalization (wind, snow, earthquake). Project-level matching pre-filters the search space before any component analysis.

**Transcripts:** [Tue AM](https://app.fireflies.ai/view/01KFZ9MSE9WZ179TAYYC30C6JC) (component-level parameters, yellow fields) + [Tue PM](https://app.fireflies.ai/view/01KFZR74V0N0P1S589KBKZCBYH) (search/return parameters, variance tolerances 3-10%) + [Wed Follow-up](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4) (verified criteria list, ranking approach)

### Part 4: Validation Design — Can we validate it?

**Validation process** (agreed in Workshop 2):

1. REKERS provides 10 reference projects (Anfragen + Angebote + files) — ✅ delivered (14 actually)
2. Similarity criteria defined with Herr Sasse — ✅ done (9 criteria from Workshop 2)
3. REKERS provides **Evaluierungsliste** (test queries + expected matches) — ❌ not delivered
4. Marius provides CSV template for Evaluierungsliste — ❌ not created (→ meeting agenda deliverable)
5. AI processes test queries → returns ranked list
6. Compare AI ranking vs expected ranking

**Two-phase validation:**
- **Phase 1 (binary):** "Hier ist die Anfrage, das muss das Ergebnis sein." Given a test query, does the system return the expected project? Pass/fail per query. Goal: prove the AI can find correct matches at all.
- **Phase 2 (ranking quality):** "Jetzt musst du mir drei mit Ranking 80, 70, 60 Prozent ausliefern." Given a test query, does the system return matches in the expected order with meaningful scores? Top-K ranked list, 0-100% scores, 60% cutoff below which results aren't useful. Goal: prove the AI can distinguish between strong and weak matches.

**Evaluierungsliste template** (must-have columns):

| Column | Type | Description |
|--------|------|-------------|
| `Test_Anfrage_ID` | ID/text | The query being tested |
| `Expected_Match_1` | ID | Best expected match (Anfrage ID) |
| `Red_Herring` | Boolean | TRUE = "nothing found" is the correct answer |
| `Match_Reason` | Text | Which criteria drive the similarity (or why no match) |

**Red herrings** [TOUCHED — needs deep dive]: Test queries where the correct answer is "nothing found." The system must be able to say "tut mir leid, nichts gefunden" instead of forcing irrelevant matches. "Ist schlecht programmiert, wenn die KI nicht sagen kann, tut mir leid, nichts gefunden." How to design and select red herrings needs further exploration.

**Key assumptions:**
- Leave-one-out valid (use 9 as reference, query the 10th)
- Results = Top-K ranked list with 0-100% scores, 60% cutoff
- AI must handle "nothing found" case
- REKERS should provide conscious test queries (not timestamp reconstruction)

**Open question (→ meeting agenda):** What does a new request actually contain at arrival time? Determines which criteria are available for matching input.

**Client dependency:** Evaluierungsliste content from REKERS (→ meeting agenda).

**Transcript:** [Wed Follow-up — Priorisierung & UI](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4) (ranking thresholds, parameter weighting, test case design) + [Wed AM](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (Evaluierungstabelle concept, Herr Sasse's role)

### Part 5: Infrastructure Assessment — Where does it run?

Target: IBM Power 10 (~20 TOPS per chip). Alternative: hybrid approach (external GPU training, local Power 10 inference). Only relevant after Go/No-Go decision — does not affect feasibility assessment itself.

**Transcripts:** [Wed AM](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (Power 10 performance, ~20 TOPS) + [Tue AM](https://app.fireflies.ai/view/01KFZ9MSE9WZ179TAYYC30C6JC) (hybrid GPU/Power approach)

### Part 6: POC Scope Definition — What does the POC look like?

**Known:** 10 historical projects, project-level matching only. Component-level matching deferred. Batch processing model (overnight AI, morning review).

**Unknown:** Contract terms, effort estimate, timeline — derived from feasibility assessment results. Next step after Go: create POC contract based on findings.

**Transcript:** [Wed AM](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (10-project scope, project-level first, batch processing model)

### Part 7: Local Model Feasibility — Can Power 10 produce Kriteriennachweis?

The Kriteriennachweis documents (Part 2) were produced using Claude Opus via the Ralph playbook — a frontier model with multimodal vision, running on Anthropic's API. REKERS asked: can this be done locally on Power 10?

**What we're testing:** Whether a local vision-language model on IBM Power 10 can extract the same 9 criteria from the same project PDFs and produce comparable Kriteriennachweis output. Not a chat system — batch processing with tolerance for longer runtimes.

**UNDEFINED — model selection:** Initial research identified multimodal vision-language models available as open weights with Ollama/GGUF support: Qwen2.5-VL (7B/32B), Qwen3-VL (8B/32B), InternVL3 (8B/14B). Multimodal is required — the 5x criteria extraction advantage from visual PDF reading (Part 2) depends on it. Model selection needs deeper evaluation: ppc64le compatibility, German language quality, prompt eval performance on Power 10, and actual output quality against the existing Kriteriennachweis. RAM sizing (~10-12 GB at 7B Q4_K_M) fits Power 10 comfortably.

**Architecture:** Script-driven pipeline. Python orchestrates file inventory, PDF rendering, and output formatting. The vision model handles only the extraction step (PDF page → criteria). This separates deterministic logic from model inference, making the model swappable.

**Initial performance projection:**

| Platform | Prompt Eval | 1 Project (50 pages) | 14 Projects |
|----------|------------:|---------------------:|------------:|
| Power 10 — current (5 CPUs, 55 tok/s) | 55 tok/s | ~60 min | ~14 hours |
| Power 10 — optimized target (500 tok/s) | 500 tok/s | ~6.7 min | ~1.5 hours |
| M3 Ultra 512GB (Metal GPU, reference) | 1,470 tok/s | ~2.3 min | ~32 min |

Assumptions: ~4,000 image tokens per PDF page, 50 priority pages per project. Primary bottleneck is prompt eval for image input — token generation speed is adequate. Known ppc64le issue: llama.cpp defaults to 1 thread on Power architecture (GitHub #9623), must be explicitly configured.

**Strategic context:** This benchmark is an internal investment trigger for Thomas and Ulrich. The performance table makes the case visible: current speed processes 14 projects overnight, optimization brings it to under two hours. Feeds into the IBM Power AI Inference Optimization Initiative (#713) and the broader product line question: Power 10 as deployment target for operations below a complexity threshold, M3/M5 Ultra for operations above it.

**Quality benchmark:** A/B comparison — Claude output (existing Kriteriennachweis) vs Power 10 output (same project, same PDFs). At least one project, preferably more. Quality is subjective; criteria hit rate provides a quantitative anchor.

**UNDEFINED — deeper investigation needed** (→ next extraction pass):
1. Model candidates — ppc64le validation, German output quality, actual benchmarking on Power 10
2. PDF-to-image rendering on ppc64le — library availability (poppler)
3. Ollama vision API on ppc64le — multimodal endpoint untested on Power architecture
4. Prompt design — Ralph playbook prompts need adaptation for smaller model
5. Extraction scope — PDFs only, or include EML body and XLSX reading?
6. Multi-turn vs single-turn — iterative extraction (like Ralph) or one-pass?
7. Runtime tolerance — Ulrich validation: what processing time is acceptable?
8. Libel engagement — Power 10 hardware optimization, timing TBD

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

**Sessions:**

- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects/fc9f193e-2d54-4ea9-bcc1-5dfd2dd35f2d.jsonl` (initial design doc creation)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/00e6a51f-cd0d-432f-b5f4-f2a41dd07a4d.jsonl` (data deep dive, criteria mapping, validation design)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/cb7381c0-65f8-42cd-bf0a-a6a09bbfe357.jsonl` (Part 1 extraction pass — 16 uncertainties resolved)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-REKERS--poc/772b96f8-d646-428c-bde6-9eeddf0418b5.jsonl` (Part 2 extraction pass — 12 uncertainties resolved, evidence template formalized)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-REKERS--poc/a661e34f-b89f-48bf-bc6c-bd590536fae4.jsonl` (Part 7 extraction pass — local model feasibility, Power 10 benchmarks)
