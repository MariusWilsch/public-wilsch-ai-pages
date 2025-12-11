# DS-Kit Navigation RAG System - Festpreisangebot
[[client-iitr]]

---
publish: true
---

## Status

**FINALIZED** - Ready for client

---

## Pflichtenheft

Full technical specification: [Pflichtenheft DS-Kit Navigationssystem](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/pflichtenheft-dskit-navigation)

---

## Scope Summary

- RAG-System für DS-Kit Navigationsfragen ("Wo finde ich X?")
- Streaming-Antworten in Echtzeit (Typesense v29 native RAG)
- Konversationsverlauf mit Follow-up Fragen
- Chat-Interface via OpenWebUI
- Verarbeitung: PDF-Leitfaden + Excel Q&A + 56 ZIP-Vorlagen
- Text-only Ausgabe (sprachfähig)
- Evaluation: >90% korrekte Antworten auf Testfragen.xlsx

---

## Festpreis

| Work Package | Hours |
|--------------|-------|
| Vision preprocessing (56 templates) | 3h |
| Typesense v29 + indexing | 7h |
| OpenWebUI integration | 4h |
| Output formatting + prompt engineering | 4h |
| Evaluation + iteration (>90% target) | 13h |
| Deployment + documentation | 4h |
| Handoff + support | 1h |
| **Total** | **36h** |

**Festpreis: EUR 2.880** (36h × EUR 80/h)

---

## Acceptance

- Binary acceptance: >90% correct answers on Testfragen.xlsx
- Delivery includes iteration until threshold achieved

---

## Out of Scope

- Images/screenshots in responses
- Document linking / URL references
- OCR for scanned documents
- Keycloak authentication
- Navigation path visualization
