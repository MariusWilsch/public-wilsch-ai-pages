---
publish: true
---

# DS-Kit Masterfragen — Typesense Consolidation Design

[[client-iitr]]

Consolidating the Masterfragen (MF) GDPR Q&A system from a legacy Qdrant-based pipeline to the shared Typesense infrastructure, with its own collection, pipeline filter, and OpenWebUI model — completing the three-project serving architecture on IITR-STAGING.

---

## Problem Statement

IITR's DS-Kit serves ~3,500 customers with a "Masterfragen" feature: 22 pre-defined GDPR Q&A pairs covering common data protection questions (cookie banners, AV contracts, data breach procedures, employee training frequency). Customers ask these questions repeatedly — the Masterfragen system provides instant, pre-verified answers.

The current MF pipeline runs on a legacy stack: Qdrant vector store, Flask proxy, and `intfloat/multilingual-e5-large` embeddings. This stack is dead — no active users, no maintenance, separate infrastructure from the other two IITR projects (Navigation and Court Judgments) which already run on Typesense with BGE-M3 embeddings.

MF data (22 Q&A entries) was temporarily ingested into the Navigation collection (`dskit_navigation`) during #1192 to improve Navigation test scores. This is a data injection, not an architecture — MF needs its own pipeline to be queryable as a distinct product in OpenWebUI.

**This is NOT:**

| Concern | Handled by |
|---------|-----------|
| Navigation system (chapter references) | DS-Kit Navigation design doc |
| Court Judgments (DSGVO rulings) | CJ Re-indexing Pipeline design doc |
| PII Pseudonymization | #1100 (OpenWebUI-level, not pipeline) |
| Ingestion into Navigation collection | #1192 (completed, temporary) |

**Preconditions:**

- Three-project Typesense architecture established (Navigation + CJ operational)
- Shared infrastructure running: Ollama (Qwen 3.5 9B), TEI (BGE-M3 1024d), Typesense, Langfuse — all on IITR-STAGING
- Mono-repo consolidated: `iitr-platform/masterfragen/` exists with legacy code
- Source data available: 22 Masterfragen CSV (Stellmacher, 2026-01-27)
- VPN split tunnel required for staging access — FortiClient VPN blocks Claude API traffic (implementation blocker, not design blocker)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Masterfragen is a standalone product on staging: queryable via its own OpenWebUI model, backed by its own Typesense collection, with Langfuse observability — completing the three-project serving architecture. |
| **Success** | LLM judge test harness scores 22/22 against the ground truth Q&A pairs. MF model returns GDPR answers matching the pre-verified content. Langfuse traces are isolated in a dedicated MF project, distinct from Navigation and CJ. |
| **Done test** | Stellmacher selects the "Masterfragen" model in OpenWebUI, asks "Wo finde ich Mustervorlagen für AV-Verträge?", receives "Eine Vorlage... erhalten Sie in Kapitel 05. des Datenschutz-Kits" — without developer involvement. |

---

## Approach

Five parts, ordered so each builds on the previous. Parts 1-3 produce the searchable collection. Part 4 verifies quality. Part 5 handles cleanup.

### 1. Data Inventory

MF has a single data source: 22 Masterfragen Q&A pairs provided by Stellmacher (2026-01-27). Each entry contains an ID, a customer question, and a pre-verified answer.

| Property | Value |
|----------|-------|
| **Source file** | `Fragen Navigationssystem.xlsx` (Stellmacher) |
| **Entries** | 22 Q&A pairs |
| **Language** | German |
| **Format** | Excel: ID, Frage, Antwort columns |
| **Content type** | GDPR support Q&A — direct answers, chapter references, dskit@iitr.de redirects |
| **Size** | ~50 KB |

**Answer categories (observed from data):**

| Category | Count | Example |
|----------|-------|---------|
| Chapter reference | 8 | "...erhalten Sie in Kapitel 05. des Datenschutz-Kits" |
| Support redirect | 7 | "Bitte wenden Sie sich an dskit@iitr.de" |
| Direct answer | 5 | "Die Datenschutz Schulung sollte mindestens einmal im Jahr erfolgen" |
| External link | 2 | "https://www.iitr.de/datenschutz-tools" |

**No additional sources needed.** Unlike Navigation (PDF + templates + web chapters + Q&A) and CJ (2,635 court rulings), MF's scope is deliberately small: 22 curated answers that represent the complete Masterfragen knowledge base.

### 2. Ingestion Pipeline

Convert the 22 Q&A pairs from Excel to a searchable Typesense collection. No chunking needed — each Q&A pair is one document.

**Pipeline:**
```
Fragen Navigationssystem.xlsx
  → masterfragen/scripts/convert_to_jsonl.py (Excel → JSONL)
  → masterfragen/scripts/ingest.py (embed via TEI + upsert to Typesense)
  → dskit_masterfragen collection (22 documents)
```

**JSONL intermediate format** (matches Navigation/CJ pattern):
```json
{
  "id": "mf-001",
  "content": "Frage: Wo finde ich die Texte für die Webseitendatenschutzerklärung?\n\nAntwort: Texte für die Webseiten-Datenschutzerklärung finden Sie in Kapitel 8 des Datenschutz-Kit.",
  "title": "Webseitendatenschutzerklärung",
  "source_type": "masterfrage",
  "question_id": 1,
  "answer_category": "chapter_reference"
}
```

**Typesense schema:**
```json
{
  "name": "dskit_masterfragen",
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "content", "type": "string"},
    {"name": "title", "type": "string"},
    {"name": "embedding", "type": "float[]", "num_dim": 1024},
    {"name": "source_type", "type": "string", "facet": true},
    {"name": "question_id", "type": "int32"},
    {"name": "answer_category", "type": "string", "facet": true}
  ]
}
```

**Embedding:** BGE-M3 via shared TEI at `rag-staging-tei:80` (1024 dimensions). Same endpoint used by Navigation and CJ.

**Idempotency:** Fixed IDs (`mf-001` through `mf-022`). Typesense upsert mode — re-running updates existing documents.

**Data in git:** Excel file committed to `masterfragen/data/`. The commit IS the audit trail. No GHA trigger needed for 22 static entries — manual one-time ingestion on staging.

### 3. Serving Layer

MF follows the standard serving pattern: pipeline filter + system prompt + OpenWebUI model. One filter per project, one prompt per project, one model per project.

#### Pipeline Filter

`masterfragen/pipelines/masterfragen_hybrid_rag_filter.py` — same retrieval logic as Navigation and CJ, different collection and prompt.

| Component | Value |
|-----------|-------|
| **Collection** | `dskit_masterfragen` |
| **Search** | Typesense hybrid (BM25 + BGE-M3 vector, alpha configurable) |
| **Embedding** | TEI at `rag-staging-tei:80` (query-time) |
| **LLM** | Qwen 3.5 9B via Ollama (shared, `think:false`) |
| **Prompt** | `masterfragen/prompts/dskit_masterfragen.md` (loaded via `__file__`-relative path) |

**Langfuse integration:** Use `@observe()` decorator from Langfuse SDK — not the manual context manager pattern used in Navigation/CJ (#1252 witness finding). Trace both `inlet` (retrieval) and `outlet` (LLM response) for full lifecycle observability. Trace name: `masterfragen`.

**Langfuse project:** Dedicated Langfuse project for MF (`iitr-masterfragen`), separate from Navigation and CJ. Each pipeline gets its own API keys — resolving the trace leakage found in #1252 where CJ traces appeared in Navigation's Langfuse project.

#### System Prompt

GDPR Q&A specialist prompt. Derived from the legacy prompt (`masterfragen/app/util/prompt.py`) with adjustments for the new pipeline:

- **Role:** DSGVO/Datenschutz specialist
- **Word limit:** 50-80 words (short, direct support answers)
- **Topic boundary:** Hard — refuse out-of-scope questions with template: "Diese Frage liegt außerhalb meines Fachgebiets..."
- **Language:** German only
- **No navigation hints** — unlike Navigation, MF answers don't reference DS-Kit chapters as navigation targets (some answers mention chapters as content sources, but this is answer content, not navigation UI)
- **Placeholders:** `{frage}` and `{context}` (Python `.format()` style, matching CJ pattern)

#### OpenWebUI Model

| Property | Value |
|----------|-------|
| **Name** | Masterfragen |
| **Filter** | `masterfragen_hybrid_rag_filter` |
| **Visibility** | All users (Stellmacher, Kraska, internal) |
| **Description** | DSGVO Masterfragen — häufig gestellte Datenschutzfragen |

### 4. Test Rubric

Automated evaluation against the 22 ground truth Q&A pairs. Same test harness pattern as Navigation — LLM judge, CSV output, Sonnet 4.6 as evaluator.

**Scoring:**

| Dimension | Evaluator | Scoring | Source |
|-----------|-----------|---------|--------|
| Content Accuracy | AI (Sonnet 4.6) | PASS/FAIL — semantic match against expected answer | Column C (Antwort) |
| Topic Boundary | AI (Sonnet 4.6) | PASS/FAIL — out-of-scope questions refused correctly | Prompt rules |

**No chapter reference dimension.** Unlike Navigation (where chapter references are a navigation UI element), MF answers that mention chapters do so as answer content. The Content Accuracy dimension already covers whether the chapter reference is present in the answer.

**Test set = knowledge base.** The 22 Q&A pairs serve dual purpose: they ARE the ingested data AND the ground truth for evaluation. This means 100% coverage is expected — every question should retrieve its own Q&A pair and produce a matching answer.

**Target:** 22/22 (100%). With only 22 known entries and a dedicated collection, anything less indicates a retrieval or generation failure that must be diagnosed.

**Output artifact:** CSV report with columns per dimension, uploaded to Google Drive with date and time in filename (e.g., `dskit-mf-test-2026-04-01-14-30.csv`). Two modes: internal (PASS/FAIL per dimension) and client-facing (full answers). Linked for Dev Lead witness review.

**Ownership** (per Dev Lead Witness & Review System): JA defines rubric dimensions and expected answers → SA approves rubric definition → Developer creates the LLM judge harness → Dev Lead approves test results via witness review.

### 5. Legacy Cleanup

Retire the old Qdrant-based MF pipeline. The `rag-staging` containers on IITR-STAGING are dead/unused — remove them as part of this migration.

| Action | Target | Containers | Rationale |
|--------|--------|-----------|-----------|
| **Remove** | `rag-staging` compose stack | ~4 (Qdrant, Flask proxy, Ollama-staging, OpenWebUI-staging) | Dead MF pipeline, no active users |
| **Remove** | `masterfragen/app/` | N/A (code only) | Legacy Flask proxy + Qdrant ingestion, replaced by pipeline filter |
| **Remove** | `masterfragen/docker-compose*.yml` | N/A (config only) | Old Qdrant + staging compose files |
| **Retain** | `masterfragen/data/` | N/A | Source data (Excel) |
| **Retain** | `masterfragen/pipelines/` | N/A | New pipeline filter (created in Part 3) |
| **Retain** | `masterfragen/prompts/` | N/A | New system prompt (created in Part 3) |
| **Retain** | `masterfragen/scripts/` | N/A | New ingestion scripts (created in Part 2) |

**Cleanup order:** Deploy new pipeline first (Parts 1-4), verify it works, THEN remove legacy. Never remove before the replacement is serving.

**Navigation collection cleanup:** The 22 MF entries injected into `dskit_navigation` during #1192 should be evaluated after MF has its own collection. If they still improve Navigation scores, keep them. If they add noise, remove them. This is a post-migration decision.

---

## Source

**Data:**
- [Fragen Navigationssystem.xlsx](https://docs.google.com/spreadsheets/d/1QiSnHbvuiE7hajZ30pu4WfXizVfnWHrz/edit) — 22 Masterfragen Q&A (Stellmacher, 2026-01-27)
- [Source Data Inventory (Drive)](https://drive.google.com/drive/folders/1gnxBulrnkGh-Oyly_jabofNo4SVivQhR) — INPUT/ + TEST_RUBRIK/

**Design References:**
- [DS-Kit Navigation Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-design) — Navigation system design (structural reference)
- [CJ Re-indexing Pipeline Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/cj-reindexing-pipeline-design) — CJ pipeline design (pattern reference)

**Issues:**
- [#1264](https://github.com/DaveX2001/deliverable-tracking/issues/1264) — this design doc's tracking issue
- [#1192](https://github.com/DaveX2001/deliverable-tracking/issues/1192) — MF data ingestion into Navigation (completed)
- [#1218](https://github.com/DaveX2001/deliverable-tracking/issues/1218) — MF Pipeline Filter (closed, awaiting this design doc)
- [#1252](https://github.com/DaveX2001/deliverable-tracking/issues/1252) — CJ Prompt + Langfuse (Langfuse SDK lesson)
- [#1100](https://github.com/DaveX2001/deliverable-tracking/issues/1100) — PII Pseudonymizer (out of scope)
- [#959](https://github.com/DaveX2001/deliverable-tracking/issues/959) — parent epic

**Transcripts:**
- [2026-03-22 Grooming](https://app.fireflies.ai/view/01KMB243RF95QP9ZFG9VGBWFX9) — MF Typesense consolidation discussed, "gleichen Scheiß für MF" + "Das sollte einfacher sein"

**Code (existing):**
- [masterfragen/app/](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/staging/masterfragen/app) — legacy Qdrant pipeline (to be retired)
- [navigation/pipelines/typesense_rag_filter.py](https://github.com/WILSCH-AI-SERVICES/iitr-platform/blob/staging/navigation/pipelines/typesense_rag_filter.py) — reference implementation for MF filter

**Sessions:**
- `60dc7801-db94-4eb2-b39e-b03345e96175` — this extraction pass

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under CC BY-NC-ND 4.0.

WILSCH AI SERVICES OÜ | Harju maakond, Tallinn, Sepapaja tn 6, 15551
