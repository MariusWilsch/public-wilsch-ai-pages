---
publish: true
---

# Meeting Agenda: VET DOCX + Invoice — Rohdex
[[project-rohdex]]

**Date:** Tuesday, 2026-03-11, 12:30
**Attendees:** Marius Wilsch, Konstantin Fitermann
**Duration:** ~15–20 min

## Meeting Goal

Clarify two open items: (1) whether VET output should be Word instead of Excel, and (2) invoice timing for the completed migration.

---

## Topic 1: VET Document Output Format

**Background:** The system currently generates all 9 document types as Excel files. In December 2025, Konstantin sent a Word document ("Veterinär BUSAN.docx") as a veterinary certificate example. The system previously supported Word output but was migrated to Excel-only.

**Question for Konstantin:** Does ROHDEX need the VET (veterinary certificate) output as a Word document (.docx) instead of / in addition to the current Excel output?

**Talking Points:**
- Current state: VET outputs Excel with all 7 dynamic fields (bundle count, weights, partie marks, container/seal/invoice numbers)
- The BUSAN.docx Konstantin sent matches the data we already extract — the question is output FORMAT
- If DOCX needed: Konstantin provides a Word template (or we create one based on BUSAN.docx)
- If Excel is sufficient: no change needed, #1037 closes

**Cost Estimate (if DOCX confirmed):**

| Task | Effort |
|------|--------|
| Add DOCX library + create VetDocxStrategy | ~2h |
| Template integration (if Konstantin provides template) | ~1h |
| Template creation (if we build from scratch) | ~2–3h |
| Testing with existing datasets | ~1–2h |
| Integration into document pipeline | ~0.5h |
| **Total (template provided)** | **~4–5h = €360–450** |
| **Total (template from scratch)** | **~5–7h = €450–630** |

Billed under SLA v4 (§4.1: €90/h, 30-min increments).

---

## Topic 2: Invoice for Migration

**Background:** Migration to RDX-APP-01 is complete and live since March 5. SLA v4 §1.1 scopes the migration at €720 (8h), which includes the Tara bugfix (~1h). The Tara fix (#1047) is not yet done — it requires the staging environment first.

**Question for Konstantin:** Can we invoice the €720 migration now, with the Tara bugfix billed separately under hourly T&M when completed? Or should the invoice wait until Tara is also fixed?

**Talking Points:**
- Migration is live — system processes emails on RDX-APP-01 (verified March 5)
- Tara bugfix (packing list decimal error) is scoped at ~1h but blocked on staging setup
- Option A: Invoice €720 now, Tara billed as T&M on next monthly invoice
- Option B: Wait until Tara done, invoice €720 as complete package
- SLA v4 §4.2: invoices monthly on 1st of following month, NET 14

---

## References

- [SLA v4 Wartungsvertrag](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/sla-v4-wartungsvertrag)
- [Design Doc (Hosting/Migration)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen)
- Related issues: [#1037](https://github.com/DaveX2001/deliverable-tracking/issues/1037), [#1047](https://github.com/DaveX2001/deliverable-tracking/issues/1047)
