# DS-Kit Navigation RAG System - Estimate Document
[[client-iitr]]

---
publish: true
---

## Purpose

- Internal document for client confirmation call
- Price NOT FINALIZED pending confirmation

---

## Input Sources

- **DS-Kit PDF** (25 pages): Text-only extraction, ignore images
- **FAQ Excel** (~15 Q&A pairs): Mix of DS-Kit answerable and out-of-scope business questions
- FAQ = fallback for questions DS-Kit cannot answer

---

## Infrastructure

- Fresh standalone deployment on IITR-STAGING
- Does not touch existing Urteile RAG system

---

## Output Format

- Text-only responses (sprachfähig for TTS)
- No links, no images, no document attachments

---

## LLM

- Model selection TBD (part of this work)

---

## Estimate

| Work Item | Hours |
|-----------|-------|
| Infrastructure deploy | 2-4 |
| DS-Kit PDF processing | 4-6 |
| FAQ processing | 2-3 |
| Ingestion | 2-3 |
| RAG pipeline + prompt | 4-6 |
| Model selection/testing | 2-4 |
| Testing + evaluation | 2-4 |
| Revisions (1-2 rounds) | 4-8 |

**Total**: 22-38 hours

---

## Pricing

- Rate: EUR 80/hour
- Range: EUR 1,760 - EUR 3,040
- Middle: EUR 2,400 (30h)
- Type: Festpreisangebot (fixed price)

**STATUS: NOT FINALIZED**

---

## Out of Scope

- Images/screenshots
- Document linking
- Full Technical Design Doc features (OCR, navigation paths, Keycloak)

---

## Client Confirmation Checklist

- [ ] DS-Kit PDF correct version?
- [ ] FAQ Excel complete?
- [ ] Text-only output confirmed?
- [ ] Price range acceptable?
