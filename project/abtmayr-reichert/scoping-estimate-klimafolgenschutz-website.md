# Scoping Estimate: Klimafolgenschutz Website
[[client-abtmayr-reichert]]
---
publish: true
---

Ballpark estimate (Richtpreisangebot) for the Klimafolgenschutz website build. Non-binding indicative pricing within the T&M Rahmenvertrag (€120/hr). All 7 sub-issues scoped via SURFACE/RESOLVE methodology.

---

## Estimate Summary

| Sub-Issue | Description | Estimate (hrs) | Status |
|-----------|-------------|---------------|--------|
| #757 | Foundation + CMS + Deploy Pipeline | 8-14 | Buildable |
| #758 | Design System + Branding | 4-8 | Buildable |
| #759 | Static Sections + Legal Pages | 4-6 | Buildable |
| #760 | Projekte Section (CMS-Connected) | 4-6 | Buildable |
| #761 | Mitgliedschaft Section (Forms + Pricing) | 6-10 | Buildable |
| #762 | Stripe Payment Integration | 8-12 | BLOCKED (Vereinsregister) |
| #763 | Zertifizierung Section | 4-6 | DEFERRED (content from Michael) |
| — | Cross-cutting (QA, responsive, cookies, analytics, revisions) | 8-12 | — |
| **Total** | | **~46-74 hrs** | |
| **At €120/hr** | | **~€5.520 - €8.880** | |

Design work already invested: ~3,5 hrs (alignment value).

---

## Resolved Uncertainties

### #757 Foundation + CMS + Deploy Pipeline (8-14 hrs)

**Server state (verified Feb 13):** WILSCH-AI-SERVER has Docker v29, Caddy (systemd), PostgreSQL (native), Node v25.3. Disk at 27% after cleanup (318GB free). 27 containers running, all healthy.

**Payload CMS v3:** First deployment. Full Next.js application running in Docker container. PostgreSQL adapter. Caddy reverse proxy for admin subdomain. Collection schemas (projects, memberships, content, legal) = significant design effort (3-5 hrs within this estimate).

**Vike (React + Vite SSR/SSG):** First time for team. Learning buffer ~4-6 hrs across all frontend sub-issues. WHY Vike was chosen:
1. React has no native SEO — Google sees empty div
2. Klimafolgenschutz needs discoverability — municipalities search for climate solutions
3. Vike adds SSG where SEO matters, keeps react-router for interactive sections
4. Preserves existing React frontend guidelines — no Next.js rewrite needed

**Vercel:** Standard deployment pipeline (~1-2 hrs). **DNS:** Client dependency (Michael provides Ionos credentials, ~0.5 hr setup).

### #758 Design System + Branding (4-8 hrs)

tweakcn setup trivial (npx install). CSS variables convention added to [React frontend guidelines](https://github.com/veloxforce/velox-global-adrs/commit/ba5e956). Century Gothic = not a free web font, substitute needed. Component library: 5-8 core components. Michael approval gate for color selection.

### #759 Static Sections + Legal Pages (4-6 hrs)

- Hero with 2 CTAs (Mitglied werden + Beratung anfragen)
- "Beratung anfragen" = mailto link (simplest, no backend)
- Prozess 5-step visualization = implementation decision (David)
- e-Recht24 templates (Impressum, Datenschutz) = standard integration
- Copywriting: GenSpark prototype + AI generation
- Newsletter: **OUT of scope**

### #760 Projekte Section (4-6 hrs)

5 CMS-connected tiles. Images: Unsplash + AI (OpenRouter). Initial content seeded from design doc descriptions. Summary-only tiles in v1 (no detail pages).

### #761 Mitgliedschaft Forms + Pricing (6-10 hrs)

Pre-Stripe: email notification to Michael on form submit. 3-4 placeholder municipality tiers (CMS-managed, Michael confirms brackets later). Form validation = standard. Pricing display = implementation decision.

### #762 Stripe Payment Integration (8-12 hrs) — BLOCKED

3 payment flows: SEPA Lastschrift (individual recurring), Stripe Invoicing (municipality), Card (donations). Webhook handling: ~3-4 hrs for 3 handlers. Spendenbescheinigung: 2 extra fields for donations ≥€100 (~1 hr). Build in test mode first, production cutover when Vereinsregister clears.

### #763 Zertifizierung Section (4-6 hrs) — DEFERRED

Content undefined — waiting on Michael. Estimated as equivalent to Projekte section (text + visualization). Flagged as uncertain.

### Cross-Cutting (8-12 hrs)

- Responsive/mobile-first: assumed for single-scroll site
- Cookie consent: ~1-2 hrs (library-based)
- Analytics: ~1 hr
- QA/testing: 10% buffer on total
- **Revision rounds:** 2 rounds for whole site, additional rounds = T&M

---

## Rate Strategy

| Role | Rate | Notes |
|------|------|-------|
| Architect (Marius) | €120/hr | Design work, alignment meetings |
| Developer (David) | €120/hr baseline | Negotiation room: developer rate could flex down |

Wiggle room: if Michael pushes back on total, developer rate can be adjusted. Architect rate stays at €120/hr.

---

## Scope Extensions (Not Included)

Michael proposed two extensions (Feb 13 message) — NOT in this estimate:

1. **Interactive needs assessment page** — municipalities check off existing infrastructure (heat protection, digitalization, sensors). Diagnostic questionnaire feeding into membership flow.
2. **"Moorhuhn" fun/engagement page** — gamification element.

Both need spec-design pass before estimating. Separate issue to be created.

---

## Infrastructure Costs (Recurring)

*To be detailed in next session.*

- Vercel: Free tier likely sufficient for initial traffic
- VPS (Payload CMS share): portion of existing WILSCH-AI-SERVER cost
- Domain: klimafolgenschutz.com at Ionos (Michael's existing cost)
- Stripe fees: per-transaction (no monthly fee)

---

## Key Links

| Resource | Link |
|----------|------|
| Design Doc | [Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website) |
| Meeting Agenda (payment) | [Zahlungseinrichtung](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/meeting-agenda-klimafolgenschutz-payment-setup) |
| Framework Contract | [Rahmenvertrag T&M](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/rahmenvertrag-tm-2025-12-16) |
| React Frontend Guidelines | [velox-global-adrs](https://github.com/veloxforce/velox-global-adrs/blob/main/README-REACT-FRONTEND.md) |
| Parent Issue | [#726](https://github.com/DaveX2001/deliverable-tracking/issues/726) |

---

## Source

- Scoping session: `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL__soloforce/5b5fc3d1-06ae-4f97-a8c4-945ade6484b6.jsonl`
- Meeting transcript (Feb 9): [Google Drive](https://drive.google.com/file/d/1PtCYWZKiVPwz3Llykvju89R2suLZIk1W/view)
- Design doc methodology: [hippocampus](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/design-doc-methodology)
- Server cleanup: Disk 93% → 27% (Feb 13 — deleted unused Docker images)
