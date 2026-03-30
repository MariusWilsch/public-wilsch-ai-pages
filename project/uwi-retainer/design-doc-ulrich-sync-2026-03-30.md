---
publish: true
---

# Design Doc: Ulrich Sync — 30. März 2026
[[client-wilsch-group]]

Captures decisions, deferred items, and action commitments from the 9-topic Ulrich sync meeting.

---

## Problem Statement

Nine operational topics accumulated across the UWI / Wilsch Group retainer — contract renewal, open invoices, seminar follow-up, data security, NDA, IBM Power assessment, product packaging, newsletter strategy, and international expansion. Each required stakeholder input from Ulrich and Thomas that couldn't be resolved asynchronously.

**Preconditions:**
- Current support contract (Dezember 2025) expires 30. März 2026 — no successor model ready
- FSO Seminar completed 27. März with 4 interested contacts requiring follow-up
- DGX Spark hardware acquired but ownership/billing structure unresolved
- Multiple deferred items from prior syncs (NDA, data security, newsletter)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | All stakeholders aligned on decisions, ownership, and timelines for each of the 9 agenda topics + bonus items |
| **Success** | Each topic has a clear verdict — resolved, deferred with next step, or buried — readable by someone who wasn't in the room |
| **Done test** | Can Ulrich, Thomas, Marius, and David each identify their action items and deadlines from this document alone? |

---

## Approach

Nine agenda topics, ordered as discussed. Each answers its "Zu klären" questions with a verdict: ✅ resolved, ⏳ deferred, 🪦 buried, ❌ not discussed.

### Part 1: Vertrag — Rahmenvertrag ab April ⏳

The current retainer was extended by one month at €1.250. No successor contract model was agreed.

Roles were clarified: Ulrich takes 10% sales commission with no project ownership or liability. Thomas serves as the billing entity (Fakturadresse). Marius and Thomas split the remaining 90% per project — exact split not yet defined.

Marius will develop a "Design Partner" model (retainer + success fee, inspired by Palantir's FDI approach) within two weeks.

| Action | Owner | Timeline |
|--------|-------|----------|
| Design-Partner-Modell ausarbeiten | Marius | 2 Wochen |
| Vertrag + NDA besprechen | Alle drei | Donnerstag 07:00 |
| Produktlinien-Diskussion | Marius + Ulrich | Samstag (Ostern) |

**Undefined:** Contract scope and start date → [Donnerstag 07:00 follow-up]

### Part 2: Rechnungen — Rekers + DGX Spark ✅

**Rekers:** Marius prepares the invoice at 40% to WPH, pending Thomas's confirmation of the split (today/tomorrow). Ulrich calls Witak directly to establish a small decision-making team rather than full group meetings.

**DGX Spark:** Thomas owns the hardware 100%. Marius re-invoices Thomas (Weiterberechnung from his Estonian company). The refinancing model is €500/month baked into project billing, already active with AWO. The DGX unit moves to Thomas's datacenter in April; VPN and SSH access for Marius follows.

| Action | Owner | Timeline |
|--------|-------|----------|
| Confirm Rekers 40% split | Thomas | Heute/morgen |
| Rekers-Rechnung vorbereiten + senden | Marius | Nach Thomas-OK |
| Witak anrufen (Entscheider-Team) | Ulrich | Diese Woche |
| Weiterberechnung DGX an Thomas | Marius | — |
| DGX in Rechenzentrum umziehen | Thomas | April |

### Part 3: FSO-Seminar — Follow-up ✅

The seminar produced 3–4 interested contacts: Pöckemöller, Hasloff, Arndt, and one more (name not recalled). Ulrich follows up all of them next week by phone, targeting DGX Spark orders.

Separately, Ulrich will issue 10 trial website access accounts to seminar participants and track usage and willingness to pay. Thomas and David are skeptical about paid content.

| Action | Owner | Timeline |
|--------|-------|----------|
| Seminar-Kontakte nachtelefonieren | Ulrich | Nächste Woche (Mi) |
| 10 Website-Test-Zugänge vergeben | Ulrich | Nächste Woche |

**Undefined:** Next seminar date — not discussed.

### Part 4: Kundendaten auf GitHub ⏳

Not discussed as a standalone topic. Surfaced indirectly during the DGX Spark discussion: GitHub is explicitly excluded for customer data. Self-hosted GitLab was floated as an alternative. No decision made.

**Undefined:** Repository solution, hosting, migration plan → Marius + Thomas separate discussion.

### Part 5: NDA für David ❌

Not discussed. Meeting ran out of time. Deferred to Donnerstag 07:00.

**Undefined:** All questions (wait for Lemke vs. Ulrich reviews draft directly) → [Donnerstag 07:00 follow-up]

### Part 6: IBM Power / Kernel-Entwickler 🪦

Unanimously buried. Thomas identified a fundamental architecture mismatch — the chip was not designed for AI workloads. No activity planned for 12–24 months. Possible reassessment if Power 12 ships with a GPU-friendly design.

### Part 7: KI aus der Box + Rekers-Preis ✅

**KI aus der Box:** Orders will be processed through Wilsch KG's existing Warenwirtschaft system — Ulrich sets up 4 articles, Busch generates order confirmations. Customers want a formal quote before ordering, not a direct Stripe checkout. David builds a PDF quote generator (Markdown → PDF). Two product lines to keep it simple: (1) expertise booking, (2) DGX Spark hardware + setup.

→ [Issue #1334](https://github.com/DaveX2001/deliverable-tracking/issues/1334) — PDF Quote Generation

**Rekers price estimate:** No pricing work until the Rekers invoice (Part 2) is sent and paid. The Step 2 offer will be derived from AWO's existing Step 2 offer as a baseline. Next Rekers meeting expected in 2–3 weeks after Easter.

| Action | Owner | Timeline |
|--------|-------|----------|
| PDF-Angebotsgenerator erstellen | David | — |
| 4 Artikel in Warenwirtschaft anlegen | Ulrich/Busch | — |

### Part 8: Newsletter 🪦

Deferred for 3–6 months. No capacity, no owner. Marius closes the issue. The 10 trial website accounts (Part 3) serve as a lightweight pilot for content distribution instead.

### Part 9: Malaysia-Expansion — Kate ⏳

Kate's rate of €190/h was deemed too high for what is currently a contact-brokering role. Ulrich will contact Kate directly by Friday or Saturday to discuss terms.

| Action | Owner | Timeline |
|--------|-------|----------|
| Kate direkt kontaktieren | Ulrich | Fr/Sa |

**Undefined:** Whether the engagement proceeds at all — depends on Ulrich's conversation with Kate.

### Bonus: AWO / DGX-Setup

The QSFP 400Gbit cable arrived. Marius tests and installs the model by Tuesday/Wednesday. AWO customer meetings shift to a 2-week cadence; next meeting ~14. April after technical validation.

### Bonus: Placetel-Ersatz

Frank Lasinski offers an alternative at €9.90/person (vs. current €55/person). He can set it up for the entire team. Topic closed — Ulrich follows up with Frank.

### Bonus: DGX Spark als Miet-Ressource

€500/month rental model for customers, already active with AWO. Will be included in the Rekers Step 2 offer.

---

## Source

- **Transcripts:** [Transcript 1](https://app.fireflies.ai/view/01KMT67DGFRWFQABVPQXJS4TG8) · [Transcript 2](https://app.fireflies.ai/view/01KMZHD5KNYE528RKCYD02V9J8)
- **Agenda:** [Meeting-Agenda Ulrich Sync 30. März](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-ulrich-sync-2026-03-30)
- **Tracking:** [#1312](https://github.com/DaveX2001/deliverable-tracking/issues/1312)
- **Session:** 8efe0543-2f51-483e-8c0c-f0184380ec4e

🤖

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

