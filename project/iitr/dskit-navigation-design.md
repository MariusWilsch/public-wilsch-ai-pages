---
publish: true
---

# DS-Kit Navigation — Production Readiness Design
[[client-iitr]]

Taking the DS-Kit Navigation RAG system from prototype (17/29 accuracy) to production-ready (28/31 target) through data expansion, re-indexing, and dual-answer formatting.

---

## Problem Statement

IITR's DS-Kit product serves ~3,500 customers who regularly contact support with recurring questions (27-28/day per Stellmacher). A navigation system reduces this support load by answering common questions automatically.

A prototype exists (Phases 1-4 complete): working RAG pipeline on IITR-STAGING scoring 17/29 on the test harness. All tuning levers have been exhausted — the gap is data and answer design, not infrastructure.

This project takes the navigation system from prototype to production-ready by:
- Expanding the knowledge base (website extraction + expanded Masterfragen)
- Implementing the dual-answer format (general info + navigation guidance)
- Deploying observability for continuous improvement

**Preconditions:**
- Prototype pipeline operational on IITR-STAGING (Typesense + TEI + OpenWebUI + Pipelines, 70 chunks indexed)
- Expanded data delivered by Stellmacher (Jan 27): 22 Masterfragen (up from 14), 31 test questions with source references, pricing abstracted
- Website extraction approved by Stellmacher (core.iitr.de chapters 1-12)
- Dual-answer format decided in client meeting (2026-01-15): every answer = general info + "find this in Chapter X" navigation guidance
- Developer (Mohamed) implements from this design doc

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Navigation system is production-ready: accurate answers with navigation guidance on the expanded test set |
| **Success** | Test harness scores 28/31 (97%) with expanded corpus. Answers include dual format (general answer + navigation guidance). Stellmacher/Kraska validate answer quality via sample review. |
| **Done test** | "Can I write a meeting agenda with open design questions?" → If NO → design is complete |

**Test harness mechanism:** Sends each question to RAG (OpenWebUI → qwen3:14B), receives response, then Claude Sonnet 4.5 (via OpenRouter) judges semantic correctness against expected answers. PASS/FAIL per question with German-language reasoning.

---

## Current Deployment

Prototype is live on IITR-STAGING. These are the DS-Kit containers:

| Container | Port | URL |
|-----------|------|-----|
| dskit-openwebui | 3007 | [dskit.iitr-cloud.de](https://dskit.iitr-cloud.de) |
| dskit-typesense | 8109 | — |
| dskit-pipelines | 9299 | — |
| TEI (embeddings) | 8110 | — |

**Access:**
- SSH: `ssh mohamed@IITR-STAGING`
- Deployment path: `/opt/dskit-rag/`
- Health check: `curl -s http://localhost:8109/health`
- Test harness: `cd /opt/dskit-rag/test-harness && uv run run_test_harness.py --api-key $(grep OPENWEBUI_API_KEY .env | cut -d= -f2)`

**Manual testing:** Open [dskit.iitr-cloud.de](https://dskit.iitr-cloud.de), pick a question from [`KI Testfragen (1).xlsx`](https://github.com/veloxforce/IITR__IITR-NAVIGATION/tree/main/.claude/tracking/issue-720) (31 test questions with expected answers), ask it, and compare the response.

**Not DS-Kit (ignore these):**

| Service | Port | What it is |
|---------|------|------------|
| rag-dev.iitr-cloud.de | 3001 | Separate RAG project |
| rag-staging.iitr-cloud.de | 3002 | Separate RAG project |
| typesense-api.iitr-cloud.de | 8108 | Legacy Typesense (unhealthy) |
| typesense-dashboard.iitr-cloud.de | 3005 | Dashboard for legacy Typesense |

---

## Approach

Three workstreams, executed sequentially with test harness validation between each. Sequential execution enables attribution of accuracy gains to specific data sources.

### 1. Masterfragen Expansion

- Convert `Fragen Navigationssystem.xlsx` → CSV (22 entries, up from 14)
- Index into Typesense with TEI embeddings (multilingual-e5-large-instruct)
- Fix Q14 VLM data loss with targeted Q&A entry (standard Docling header chunk)
- Convert `KI Testfragen (1).xlsx` → CSV for test harness (updated expected answers + source references)
- Run test harness → measure accuracy delta
- **Expected gain:** +7 questions (directly filling known data gaps: Q6, Q12, Q16, Q17, Q18, Q27, Q28)

### 2. Website Extraction

- Extract core.iitr.de chapters 1-12 as Markdown (SA credentials for portal access)
- Index website chapters into Typesense with TEI embeddings
- Run test harness → measure accuracy delta
- **Expected gain:** +2-3 questions (Q13, Q14, Q24 — content exists in DS-Kit Oberfläche)

### 3. Dual-Answer Format

- Implement dual-answer prompting in pipeline filter: general response + chapter reference navigation ("Finden Sie in Kapitel X")
- Run test harness → measure impact on accuracy
- Iterate if format changes affect scoring
- Present results + sample answers to Stellmacher for review

**⚠️ Undefined:** When to include navigation guidance — every answer, or only when a specific chapter applies? Requires discussion with Stellmacher. See meeting agenda.

---

## Source

**Data Files:**
- [`KI Testfragen (1).xlsx`](https://mail.google.com/mail/u/0/#all/19bfebdff5dcbf33) — 31 test questions with source references (Stellmacher, 2026-01-27)
- [`Fragen Navigationssystem.xlsx`](https://mail.google.com/mail/u/0/#all/19bfebdff5dcbf33) — 22 Masterfragen (Stellmacher, 2026-01-27)
- Portal: `https://core.iitr.de/tenant/3520/page/82173` — DS-Kit web interface chapters 1-12

**Codebase:**
- Pipeline: `infrastructure/dskit-rag/` (chunking, indexing, pipelines, test harness)
- Deployment: IITR-STAGING `/opt/dskit-rag/`

**Reference Documents:**
- [Test Analysis](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-rag-test-analysis) — question-by-question verification
- [IITR Materials Index](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/index)

**Transcripts:**
- [2026-01-15 Client Meeting](https://app.fireflies.ai/view/01KF0N6JDANZB4JGW9WEP4GNMC) — data gap discussion + dual-answer decision with Stellmacher/Kraska

**Design Session:**
- /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-IITR--IITR-NAVIGATION/46a000cb-3044-40d7-adaf-e30987553859.jsonl
