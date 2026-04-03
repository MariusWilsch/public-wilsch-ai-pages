---
publish: true
---

# Design Doc: KI aus der Box — PDF Quote Generation System
[[client-02_uwi]]

Self-service PDF-Angebotsgenerator für DGX Spark Interessenten — Konfiguration, Preisfindung und gebrandetes Richtpreisangebot als Download von der Website.

---

## Problem Statement

Prospects from the FSO seminar want formal quotes before purchasing DGX Spark hardware. The existing offer documents — Systemvergleich and Angebotsübersicht — are manually created in Office for IBM Power systems. The AI-in-a-Box offering has no equivalent. Seminar leads currently browse the website, get interested, but have no self-service path from "I'm interested" to "here's what it costs for my setup."

Ulrich's benchmark: Holger Scherer's IBM Power configurator — "gewisse Werte eingibt und da kommt hinten ein Richtpreisangebot raus." That tool is ugly but generates real quotes on demand. The AI-in-a-Box equivalent doesn't exist yet.

**Preconditions:**
- FSO seminar completed March 27 with 3 hot + 2 warm leads (Hasloff, Poggemüller, Amari) — all wanting ERP/DB data access
- KI aus der Box product page (V2) live on wilsch-ai.com behind Supabase login wall
- DGX Spark bundle pricing established: ~€10K purchase or €500/Mo rental
- Ulrich's reference PDFs (Systemvergleich + Angebotsübersicht) delivered as template inspiration — POW004 and POW005
- Orders processed through Wilsch KG Warenwirtschaft, not the website

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A prospect can self-configure a DGX Spark quote on the website and download a branded PDF Richtpreisangebot — no manual intervention required |
| **Success** | Ulrich gives a seminar lead a 30-day account. That lead browses the site, configures their package, and downloads a PDF professional enough for Ulrich to stand behind — the same quality standard as his IBM Power Angebotsübersicht |
| **Done test** | Can Hasloff log in, browse the KI aus der Box page, configure his DGX Spark setup, and walk away with a 2-page branded PDF quote that Ulrich would send to a customer? |

---

## Approach

### Part 1: Prospect Account System

Marius creates individual email-based accounts for each seminar lead — one account per person per company. No shared logins, no self-registration. Accounts expire automatically after 30 days.

The website already runs Supabase auth. Account creation is a manual but fast operation — Marius or David creates the account, sends the credentials to the prospect. Ulrich provides the lead's email address.

Access scope: full website. Ulrich reviewed the content and confirmed nothing needs to be restricted. Marius flagged AWO/Rekers case study data as potentially sensitive, but Ulrich approved full access.

Current leads waiting for accounts: Hasloff (hottest — his CEO wants to meet the team), Poggemüller, Amari.

### Part 2: Usage Tracking

Per-account analytics: which pages the prospect visits, how long they stay, how frequently they return. Marius mentioned heatmap-style visualization as an aspiration.

Data collection is in scope for this system. Presentation follows two tiers:

**Tier 1 (MVP) — Manual lookup:** The team checks a prospect's activity when Ulrich asks. "Was hat Hasloff sich angeschaut?" — query by email, return pages visited + timestamps + time on page. This is the launch capability — it works from day one with the collected data.

**Tier 2 — PDF generation notification:** When a prospect generates a PDF quote, the system sends an email notification to both Marius and Ulrich. PDF generation is the highest-intent signal in the funnel — a prospect who configures and downloads has moved from browsing to evaluating. This notification enables proactive follow-up without requiring Ulrich to check a dashboard.

Dashboard and heatmap visualization are explicitly deferred. The data is collected from the start — presentation can evolve independently once the core system proves its value.

Research needed: what analytics tools integrate with the existing Supabase + React stack for per-user page tracking.

### Part 3: Self-Service Configurator

Web-based configurator on the KI aus der Box page. The prospect selects their options, the system calculates a price, and generates the PDF. Inspired by Holger Scherer's IBM Power configurator — Ulrich: "Das Beste, was ich jemals am Markt gesehen habe."

Configurable dimensions:

| Dimension | Options | Effect on Price |
|-----------|---------|-----------------|
| **Pricing model** | Purchase (~€10K one-time) / Rental (€500/Mo) | Determines payment structure |
| **Hardware** | Single DGX Spark | Base hardware selection |
| **Setup scope** | Base (2 days: infra + training) / Full (5 days: + connectors + validation) | ~€2K base / ~€5K full |
| **Data connectors** | SQL, Confluence, AS/400, Vector DB (checkboxes) | Included in setup scope or extra Manntage |
| **Customer details** | Name + Company + Email | Inserted into PDF branding |

Hardware is currently single DGX Spark only. Dual DGX Spark clustering (+€5K, 256 GB unified memory, up to 397B parameter models) is architecturally validated — networking confirmed at 1ms latency, 200 Gbps via ConnectX-7. Whether model sharding is customer-ready needs confirmation from Marius before it can appear in the configurator.

Mac Studio M3 Ultra is a separate architecture entirely — disaggregated inference (DGX Spark handles prefill, Mac Studio handles decode) rather than clustering. It is not an additional hardware option for this configurator and is out of scope for this system.

Pricing carries the "Richtpreis, kann sich ändern" disclaimer — this is an indicative quote, not a binding offer.

**Undefined:** Dual DGX Spark model sharding customer-readiness — the Bundle design doc describes it as confirmed (Qwen3.5-397B), but implementation experience suggests it's not yet reliable enough to offer. Marius to confirm. → [Meeting Agenda Topic 1](#1-dual-dgx-spark-model-sharding-customer-readiness)

### Part 4: PDF Quote Output

Two-page branded PDF, generated from the configurator selections. Format follows Ulrich's IBM Power reference documents (POW004 Systemvergleich, POW005 Angebotsübersicht), adapted for DGX Spark. The reference PDFs establish the structure:

- **POW004 (Systemvergleich):** Side-by-side hardware comparison with performance charts and 5-year TCO breakdown
- **POW005 (Angebotsübersicht):** Page 1 = hardware specs + Angebotspreis, Page 2 = itemized service categories with scope descriptions + Projektpreis total

The DGX Spark PDF mirrors this: Page 1 provides comparison context (why this product), Page 2 provides the configured offer (what you get, what it costs).

**Branding elements (from reference PDFs):**
- Header: Customer name + date + Wilsch logo (teal)
- Product image
- "Unser Angebot ist freibleibend, alle Preise zzgl. Mwst." disclaimer
- Footer: Ulrich Wilsch GmbH & Co. KG contact details

**Page 1 — Why KI aus der Box (Comparison Context):**
Three-way comparison table mirroring the website's "Drei Wege zur KI" section: Cloud AI / KI aus der Box / Enterprise. Positions the prospect's choice within the market landscape. Key comparison dimensions: price, data privacy (DSGVO), database access, setup effort, ongoing control.

**Page 2 — Your Configured Offer:**
The prospect's specific selection: hardware specs, setup scope, selected data connectors, pricing model (purchase or rental), and total price. Customer name and company inserted automatically. Wilsch branding (logo, contact info, legal disclaimer). Ulrich's directive: "Verliert euch nicht in zu vielen technischen Details... so ein One Pager vor der Rückseite kommt gut an."

Technical approach: Markdown → PDF conversion pipeline. The configurator populates a Markdown template with the prospect's selections, then renders to PDF with branding applied.

### Part 5: Order Flow Integration

The PDF quote is a pre-sales artifact — a Richtpreisangebot, not a binding order. When a prospect decides to buy, the formal order goes through the existing Wilsch KG Warenwirtschaft system. Ulrich creates articles, Busch generates Auftragsbestätigungen. The website never processes payments.

The PDF's line items follow the product structure — not the Warenwirtschaft article codes. The reference PDFs (POW005) establish the pattern: hardware with Angebotspreis on page 1, itemized services with scope descriptions on page 2, Projektpreis as the total. The DGX Spark PDF uses the same pattern:

| PDF Line Item | Source | Price |
|---------------|--------|-------|
| DGX Spark Hardware | Configurator: Kauf (~€5K) or Miete (€500/Mo) | Prospect's selection |
| Einrichtung / Setup | Configurator: Base (2 MT, ~€2K) or Full (5 MT, ~€5K) | Scope-dependent |
| Data Connectors | Configurator: checkboxes | Included in setup or extra Manntage |
| Betreuungsvertrag | Optional checkbox | ab €350/Mo |
| **Projektpreis** | Calculated total | Sum of above |

How Ulrich maps these line items to Warenwirtschaft articles for formal Auftragsbestätigungen is an operational concern handled post-sale — it does not affect the PDF's design or the configurator's pricing logic.

---

## Source

- **Transcripts (2. April):** [Transcript 1](https://app.fireflies.ai/view/01KN0MEM6WSQXEM96A6E9XREQ4) · [Transcript 2](https://app.fireflies.ai/view/01KN6959QHKXZQ0ET0Q3GC51VP)
- **Transcripts (30. März):** [Transcript 1](https://app.fireflies.ai/view/01KMT67DGFRWFQABVPQXJS4TG8) · [Transcript 2](https://app.fireflies.ai/view/01KMZHD5KNYE528RKCYD02V9J8)
- **Design Docs:** [Ulrich Sync Design Doc — Part 7 + Part 11](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-ulrich-sync-2026-03-30) · [KI aus der Box Bundle](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/design-doc-ki-aus-der-box-bundle)
- **Design Docs (Pass 2):** [Hardware-Strategie: Lokale KI-Inferenz](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/hardware-strategie-lokale-ki-inferenz)
- **Reference PDFs:** POW004_Muster_SV_Baumarktkunde (Systemvergleich) · POW005_Muster_AU_Baumarktkunde (Angebotsübersicht) — in `Desktop/Wilsch-AI /02_UWI/`
- **Website:** [wilsch-ai.com/ki-aus-der-box](https://wilsch-ai.com/ki-aus-der-box) (V2, behind auth)
- **Tracking:** [#1334](https://github.com/DaveX2001/deliverable-tracking/issues/1334) · Parent [#650](https://github.com/DaveX2001/deliverable-tracking/issues/650)
- **Session (Pass 1):** 77326ad8-8fe0-4ac8-aecb-dfb0ac893fc9
- **Session (Pass 2):** ef33fa5a-4cbf-4df2-8b55-5c1febc3667d

🤖

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

