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
| Source | Processing |
|--------|------------|
| PDF Leitfaden | Docling chunking → Typesense |
| Q&A CSV (14 pairs) | Direct ingest (structured data) |
| 56 Templates | Docling chunking → Typesense |

### Data Gap
5 test questions require info not in input sources:
- Q4, Q16, Q17, Q27, Q28
- Best case accuracy: 24/29 (83%)
- Documented for client delivery discussion

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

*Created: 2026-01-11*
