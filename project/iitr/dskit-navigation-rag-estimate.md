---
publish: true
---

# DS-Kit Navigation — Zielkorridor (Werkvertrag)
[[client-iitr]]

## Status

**DRAFT** — Pending hour estimates

---

## Scope

Rebuild the DS-Kit Navigation system using PageIndex (vectorless, tree-based retrieval) on IITR's on-premise infrastructure. Full technical design: [Design Doc](dskit-navigation-design.md)

---

## Acceptance

- ≥26/29 (>90%) correct answers on Quelle-mapped test set
- Chapter-referenced answers (from PageIndex tree path)
- Stellmacher/Kraska qualitative sample review

---

## Festpreis

| Work Package | Hours |
|--------------|-------|
| Infrastructure (PageIndex + Ollama + OpenWebUI + test harness) | ?h |
| Data preparation (PDF/Word/Excel/Web → PageIndex) | ?h |
| Tree index generation + validation | ?h |
| Evaluation + iteration (>90% target) | ?h |
| Deployment + documentation | ?h |
| **Total** | **?h** |

**Festpreis: EUR ?** (?h × EUR 120/h)

---

## Out of Scope

- Dach KI / umbrella system integration
- Avatar system
- Keycloak authentication
- Images/screenshots in responses
