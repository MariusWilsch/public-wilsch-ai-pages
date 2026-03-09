---
publish: true
---

# DS-Kit Navigation — Zielkorridor (Werkvertrag)
[[client-iitr]]

## Status

**DRAFT** — Ready for review

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
| Infrastructure (PageIndex + Ollama + OpenWebUI + test harness) | 6h |
| Data preparation (PDF/Word/Excel/Web → text extraction) | 6h |
| Tree index generation + validation | 3h |
| Evaluation + iteration (>90% target) | 10h |
| Deployment + documentation | 3h |
| **Total** | **28h** |

**Festpreis: EUR 3.360** (28h × EUR 120/h)

---

## Out of Scope

- Dach KI / umbrella system integration
- Avatar system
- Keycloak authentication
- Images/screenshots in responses

---

## Design Doc — Extraction Pass Handoff

The [Design Doc](dskit-navigation-design.md) was written for the previous Typesense approach. The following sections need extraction passes before implementation can begin:

| Section | What needs updating | Priority |
|---------|-------------------|----------|
| **Approach** (3 workstreams) | Replace Typesense pipeline with PageIndex tree generation workflow | High |
| **Current Deployment** | Update container list — PageIndex replaces Typesense + TEI stack | High |
| **Success Definition** | Confirm 28/29 target still holds with PageIndex retrieval | Medium |
| **Source — Data Files** | Verify all source data accessible, add Quelle mapping from test sheet | Medium |
| **Undefined: dual-answer trigger** | Resolve "when to include navigation guidance" — PageIndex tree path may solve this naturally | Low |

**Approach:** Each section gets its own extraction pass (SCOPE → SURFACE → RESOLVE → UPDATE → ASSESS). Start with Approach — it defines the implementation path.
