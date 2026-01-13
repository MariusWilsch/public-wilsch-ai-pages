---
publish: true
---

# DS-Kit Navigation RAG - Design Document
[[client-iitr]]

*Implementation decisions (2026-01-11)*

---

## Stack Decisions

| Component | Choice | Rationale |
|-----------|--------|-----------|
| **Vector DB** | Typesense | Native conversational RAG, streaming responses |
| **Chat UI** | OpenWebUI | Standard interface, no custom frontend |
| **Orchestration** | OpenWebUI Pipelines | Connects UI to Typesense, avoids custom proxy |
| **Chunking** | Docling | Local execution, M-series GPU support, handles PDF/CSV/Word |

### Why Docling over Chunkr
- Simpler setup (`pip install docling`)
- Runs locally on MacBook M-series with Metal acceleration
- Hierarchical + hybrid chunking optimized for RAG
- 97.9% table extraction accuracy
- Supports all input formats (PDF, DOCX, XLSX)

---

## Data Architecture

### Input Sources

| Source | Processing | Status |
|--------|------------|--------|
| PDF Leitfaden | Docling chunking → Typesense | ✅ Phase 2 |
| Q&A CSV (14 pairs) | Direct ingest | ✅ Phase 3 |
| 56 Templates | ~~Docling chunking~~ | ❌ Skipped |

### Phase 4 Skip Decision (2026-01-13)

Templates provide **0-2/29 question coverage**. Analysis showed:
- Test questions ask "where to find" documents, not template content
- PDF + CSV already cover 24/29 answerable questions
- Vision AI for forms/diagrams adds complexity without accuracy improvement

**Decision:** Skip Phase 4, proceed directly to Phase 5 (Iteration).

### Data Gap Analysis (Updated 2026-01-13)

**Original assumption:** 5 questions unanswerable (Q4, Q16, Q17, Q27, Q28) → 24/29 ceiling

**Revised after rubber-duck investigation:**

| Question | Original | Actual | Evidence |
|----------|----------|--------|----------|
| Q4 (Datenschutzerklärung templates) | Gap | **Retrieval failure** | PDF 2.8 + CSV Q1 |
| Q16 (Schulungen dokumentieren) | Gap | Partial | PDF 1.5 + 2.11 |
| Q17 (AVV templates) | Gap | **Retrieval failure** | PDF 2.5 + CSV Q11 |
| Q27 (Verschlüsselung) | Gap | **True gap** | Not in sources |
| Q28 (Aufsichtsbehörde) | Gap | Partial | Only "Meldung DSB" |

**Root cause:** Vocabulary mismatch
- Test questions use "Templates" (English loan word)
- Content uses "Vorlage", "Mustervorlagen", "Texte" (German)
- Current `all-MiniLM-L12-v2` embedding doesn't recognize German synonyms

**Example:** CSV Q11 = "Wo finde ich Mustervorlagen für AV-Verträge?" → Kapitel 05
Test Q17 = "Wo finde ich Templates für Auftragsverarbeitungsverträge?" (same question!)

**Revised ceiling:** 27-28/29 (93-97%)

**Solution:** Switch to `multilingual-e5-large-instruct` (MIT, MTEB German rank 6)

---

## Content Quality Analysis (2026-01-13)

PDF structure analysis:
- **Pages 1-2:** Title + TOC (noise)
- **Pages 3-10:** UI instructions - 50% of PDF (useless for Q&A)
- **Pages 11-16:** Leitfaden chapters (actual useful content)

**Recommendation:** Index Section 2 only (pages 11-16) to remove ~60% noise

---

## Test Harness Design

```
Questions → RAG → LLM-as-Judge → Score
```

- **Semantic matching** (not exact string)
- LLM evaluates if answer contains correct key info
- Run after each data ingestion phase

---

## Deployment

- **Target:** IITR-STAGING (136.243.71.58)
- **GPU work:** Staging OR MacBook M-series
- **Method:** Docker Compose

---

*Created: 2026-01-11 | Updated: 2026-01-13*
