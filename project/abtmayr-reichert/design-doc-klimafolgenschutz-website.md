---
publish: true
---

# Design Doc: Klimafolgenschutz Website

[[client-abtmayr-reichert]]

Website design for Michael Reichert's Klimafolgenschutz association (klimafolgenschutz.com). Scope: single-scroll landing page with membership forms, payment integration, and CMS-managed content.

---

## Problem Statement

Michael Reichert's Klimafolgenschutz association needs a web presence to acquire municipal members and collect membership fees. The association is registered, the framework contract is signed, but no website exists. Without it, municipalities can't discover the association, understand its projects, or join as members.

**This is NOT:**
- The Renovierungsverein website (deferred, tracked in [#728](https://github.com/DaveX2001/deliverable-tracking/issues/728))
- A full e-commerce platform — this is a content site with membership forms and payment
- A redesign — there is no existing website to redesign

**Preconditions:**
- Framework contract signed (Jan 26 via SignNow) — [Rahmenvertrag](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/rahmenvertrag-tm-2025-12-16)
- Association registered (Vereinsregister complete)
- Domain klimafolgenschutz.com registered at Ionos (DNS access pending from Michael)
- GenSpark prototypes exist as design inspiration — Michael: *"Das ist ja einfach nur so ein erster Entwurf. Als Anregung oder so als Basis kann das ja vielleicht schon dienen."*
- Gemeinnützigkeit status: **unconfirmed** (pending verification with Michael — affects donation receipt capability)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A live website at klimafolgenschutz.com where municipalities and individuals can understand the association's projects, apply for membership, and pay — enabling Michael to operate the association digitally. |
| **Success** | **Municipality flow:** Michael sends a link → municipality independently understands the 5 projects, applies via online form, pays via SEPA/invoice. **Individual flow:** person visits site → understands mission, applies as member (€60/year), pays via SEPA/card/PayPal. **Donor flow:** visitor makes one-off donation via card/PayPal. Michael can edit all content via CMS without developer assistance. |
| **Done test** | "Can I write a meeting agenda with open design questions?" → If NO → design is complete. For the website: a test municipality and a test individual complete the membership flow end-to-end without asking Michael for help. |

---

## Approach

### Part 1: Foundation — Framework + Hosting

**Stack:** React + Vite + [Vike](https://vike.dev/) (catch-all hybrid)

- Vike catch-all route delegates to react-router — follows existing [React frontend guidelines](https://github.com/veloxforce/velox-global-adrs/blob/main/README-REACT-FRONTEND.md) with minimal deviation
- Public pages (hero, projects, about, certification) → pre-rendered SSG via Vike for SEO
- Interactive sections (forms, payment) → SPA mode
- **Frontend deployed to Vercel**

**Why Vike catch-all:** Preserves react-router patterns from existing guidelines. Adds SEO where it matters (public content pages) without rewriting the app architecture. ~80% SEO benefit with ~20% of full-framework effort.

### Part 2: Content Management — Payload CMS

[Payload CMS v3](https://payloadcms.com/) deployed on VPS (WILSCH-AI-SERVER) via Docker.

- **Caddy** reverse proxy with auto-SSL
- **PostgreSQL** (existing on server) as database
- React frontend consumes Payload REST API from Vercel
- Acquired by Figma (June 2025) — long-term maintenance assured

**What Michael edits via admin panel:** project tile text, pricing/tier info, news/events, association description, membership form labels.

**What requires developer:** structural changes, new sections, payment configuration.

### Part 3: Payment — Stripe + PayPal

> *"Wir sollten eigentlich alle Wege offen haben, alles was geht."* — Michael

**[Stripe](https://stripe.com/resources/more/membership-fees-associations-germany)** as primary processor:

| Method | Use Case | Example Fee (subject to change) |
|--------|----------|---------------------------------|
| **SEPA Lastschrift** | Individual memberships (recurring) | ~0.8% + €0.25 (max €6) |
| **Stripe Invoicing** | Municipality fees (via Überweisung) | ~0.4% per invoice |
| **Card (Visa/MC)** | One-off donations | ~1.5% + €0.25 |

*Fees are illustrative based on current Stripe pricing. Actual rates may vary and are managed in Stripe dashboard.*

**[PayPal](https://www.paypal.com/de/)** as secondary option (~2.49% + €0.35).

**Donor/Förderer flow:** Generic for all audiences — municipality, individual, or company. No membership required, no separate form. Visitor selects amount → chooses payment method (card/PayPal) → done. Same flow regardless of who the donor is.

**Stripe setup requires from Michael:** Vereinssatzung, Vereinsregister certificate, Gläubiger-ID from Bundesbank, Steuernummer, IBAN, board member ID.

**Donation receipts (Zuwendungsbestätigung):** Dependent on Gemeinnützigkeit confirmation. If confirmed, Michael handles manually via Stripe transaction records.

### Part 4: Content Structure — Single Scroll

Single-scroll landing page with anchor navigation. No sub-pages except legal.

**Sections in scroll order:**

1. **Hero** — Association mission + 2 CTAs (Mitglied werden, Beratung anfragen)
2. **Projekte** — 5 tiles (see below)
3. **Prozess** — How municipalities get matched with Klimafolgenmanager (5-step flow)
4. **Mitgliedschaft** — Two forms + payment integration (see below)
5. **Zertifizierung** — Shell with process flow visualization (criteria pending from Michael)
6. **Footer** — Contact, legal links, newsletter signup

**Legal sub-pages:** `/impressum`, `/datenschutz` (generated via [e-Recht24](https://www.e-recht24.de/))

#### Project Tiles

5 project tiles extracted from meeting transcript:

| Tile | Description (German) |
|------|---------------------|
| **E-Bike Ladestation** (Fendt Solar) | Moderne E-Bike Ladestationen für Gemeinden an Verkehrsknotenpunkten, Schulen und Radwegen. |
| **Hitzeschutz** | Fassadenbegrünung und Kühlungslösungen. Studenten aus Architektur/Bauingenieurwesen beraten vor Ort. |
| **Begrünung** | Fassadenbegrünung, Brunnenbau, Grünflächengestaltung. Klimafolgenmanager unterstützen bei Planung. |
| **Gewässerschutz & Starkregen** | Sensorik-Lösungen zur Digitalisierung von Abwassersystemen. Echtzeit-Überwachung verhindert Überflutungen. |
| **Robin Good Partnerschaft** | CO₂-Kompensation durch Baumpflanzung. Bei allen Projekten werden Bäume gepflanzt. |

#### Membership Forms

> *"Wir werden einfach nur unterscheiden beim Mitgliedsantrag: Einzelperson oder Mitgliedgemeinden. Das reicht."* — Michael

**Two forms only.** No separate Förderer form.

**Gemeinde (municipality) form fields:**
- Name, Anschrift/Adresse, Telefon
- Abteilung (department), Bereich (division)
- Gemeindegröße / Bewohneranzahl (population → determines price tier)
- Vorgesetzte Behörde (supervising authority — *"Wem ist diese Gemeinde untergeordnet?"*)
- Ansprechpartner für Umweltschutz und Klima
- Geschäftsführer der Gemeinde

**Einzelperson (individual) form fields:**

> *"Keine besonderen, die ganz normalen."* — Michael

- Name, Adresse, Telefon, Email

**Pricing (CMS-managed, examples as of Feb 2026):**
- Individual: €60/year
- Municipality: starting at ~€490/year for ≤5,000-7,500 residents, tiered upward (exact brackets TBD — see meeting agenda)

*All pricing is managed via Payload CMS. Michael can adjust amounts and tier brackets without developer involvement.*

#### Process Flow (Klimafolgenmanager Matching)

> *"Die Gemeinden müssen halt Mitglied werden, wenn sie die Zertifizierung haben wollen."* — Michael

1. Gemeinden werden Vereinsmitglieder
2. Studenten aus relevanten Fachschaften werden als Klimafolgenmanager rekrutiert
3. Matching zwischen Projekten und spezialisierten Studenten
4. Studenten erhalten Briefing und Agenda für Vor-Ort-Einsatz
5. Zertifizierung als "klimaresiliente Gemeinde" nach Abschluss

### Part 5: Branding & Design

> *"Da bin ich noch völlig offen."* — Michael on design direction

- **Color/theme:** Developer proposes 3 theme variations via [tweakcn.com](https://tweakcn.com/) → Michael picks
- **Font:** Century Gothic direction — clean, readable, no script fonts
- **Images:** Developer creative latitude (Unsplash stock or AI-generated, nature/municipality theme)
- **Logo:** Placeholder text ("Klimafolgenschutz e.V.") until Michael delivers real logo
- **Design reference:** [PIK Potsdam](https://www.pik-potsdam.de/) — *"Da kann man vielleicht noch mal sich inspirieren lassen."* Extract copywriting language and design tone for a credible German climate institution.
- **Prototype reference:** GenSpark homepage (inspiration, not blueprint) — screenshots captured in session

### Part 6: Infrastructure & Legal

- **Domain:** klimafolgenschutz.com (Ionos) — DNS credentials pending from Michael
- **Impressum:** Michael generates via [e-Recht24](https://www.e-recht24.de/)
- **Datenschutzerklärung + Cookie consent:** Michael generates via e-Recht24, developer integrates snippet
- **GDPR:** Forms collect personal data → privacy policy required, cookie consent for Stripe/PayPal

---

## Source

- **Meeting transcript (Feb 9, 2026):** [Google Drive](https://drive.google.com/file/d/1PtCYWZKiVPwz3Llykvju89R2suLZIk1W/view)
- **Meeting agenda:** [hippocampus](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/meeting-agenda-website-kickoff-2026-02-09)
- **Framework contract:** [Rahmenvertrag T&M](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/rahmenvertrag-tm-2025-12-16)
- **Issue:** [#726 — Michael Reichert Climate Association Website Development](https://github.com/DaveX2001/deliverable-tracking/issues/726)
- **Renovierungsverein (deferred):** [#728](https://github.com/DaveX2001/deliverable-tracking/issues/728)
- **GenSpark prototype:** [Homepage](https://genspark.genspark.site/api/code_sandbox_light/preview/71fefb28-2d3f-405f-b45e-ad9515477e3e/index.html?canvas_history_id=49ffc6f6-b858-47be-8824-190f8a5c20ee)
- **Design doc session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL__soloforce/bdb2cfed-1005-4068-8543-a2cde2bc4c3e.jsonl`
- **Tech references:** [Vike](https://vike.dev/), [Payload CMS](https://payloadcms.com/docs), [Stripe for German Associations](https://stripe.com/resources/more/membership-fees-associations-germany), [PIK Potsdam](https://www.pik-potsdam.de/)
