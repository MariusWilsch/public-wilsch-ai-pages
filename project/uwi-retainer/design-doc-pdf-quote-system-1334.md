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

Marius creates individual email-based accounts for each seminar lead — one account per person per company. No shared logins, no self-registration. Accounts expire after 30 days.

Authentication runs through Cloudflare Access — not Supabase. Marius adds a prospect's email address to the Access policy. The prospect receives an email one-time passcode (OTP) to log in — no passwords to manage, no Supabase accounts to create. Cloudflare Access session duration controls the 30-day window. After expiry, the prospect loses access without manual cleanup.

This decouples the website's authentication entirely from the shared Supabase project (WILSCH_AI_PROJECTS-PROD) that serves Invoice Agent, Call2Tanss, and the tax module. Prospect accounts never enter the internal auth pool — the cross-application isolation concern that existed with Supabase auth disappears by architecture.

Access scope: full website. Ulrich reviewed the content and confirmed nothing needs to be restricted. Marius flagged AWO/Rekers case study data as potentially sensitive, but Ulrich approved full access.

Current leads waiting for accounts: Hasloff (hottest — his CEO wants to meet the team), Poggemüller, Amari.

Prospect records — email, company, name, account creation date, expiry — are managed as a Payload CMS collection (see Part 6). This gives the team a single admin interface for both content management and prospect access.

### Part 2: Usage Tracking

Per-account analytics: which pages the prospect visits, how long they stay, how frequently they return. Marius mentioned heatmap-style visualization as an aspiration.

Data collection is in scope for this system. Presentation follows two tiers:

**Tier 1 (MVP) — Manual lookup:** The team checks a prospect's activity when Ulrich asks. "Was hat Hasloff sich angeschaut?" — query by email, return pages visited + timestamps + time on page. This is the launch capability — it works from day one with the collected data.

**Tier 2 — PDF generation notification:** When a prospect generates a PDF quote, the system sends an email notification to both Marius and Ulrich. PDF generation is the highest-intent signal in the funnel — a prospect who configures and downloads has moved from browsing to evaluating. This notification enables proactive follow-up without requiring Ulrich to check a dashboard.

#### Tool Selection

Two levels of tracking serve different stakeholders: page-level analytics for Ulrich ("was hat Hasloff sich angeschaut?") and element-level heatmaps for Marius (scroll depth, click patterns, drop-off points on the configurator). Both are needed — page-level first, element-level as the system matures.

**Recommended: PostHog** (self-hosted). Open-source product analytics with React SDK, per-user identification (ties to Cloudflare Access email claim via identify() API), page views, heatmaps, session replay, and funnel analysis. Self-hosting keeps all prospect behavior data in-house — DSGVO solved by architecture, not policy. Free for self-hosted deployments. PostHog runs on a separate VPS — not on Cloudflare Workers — as it requires persistent storage and background processing.

**Baseline: Cloudflare Web Analytics** (free, zero-config). Privacy-first page-level analytics with no cookies and no JavaScript tag required. Provides aggregate page views, top pages, and referrer data out of the box. Not a replacement for PostHog — it covers the "is anyone visiting?" question while PostHog answers "what is Hasloff doing?"

| Tool | Page-level | Element-level | Self-hostable | DSGVO |
|------|-----------|--------------|---------------|-------|
| **PostHog** (recommended) | ✅ | ✅ Heatmaps + session replay | ✅ | ✅ |
| **Matomo** (alternative) | ✅ | ✅ Heatmaps via plugin | ✅ | ✅ |
| **Cloudflare Web Analytics** (baseline) | ✅ | ❌ | N/A (Cloudflare-hosted) | ✅ |

Tier 1 launches with Cloudflare Web Analytics (zero setup) plus PostHog basic events for per-user tracking. Tier 2 activates PostHog heatmaps and session replay — the element-level capability Marius wants for configurator optimization.

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

### Part 6: Content Infrastructure — Payload CMS

Hardware prices are volatile — IBM raised Spyre component prices 55% in March, and DGX Spark pricing follows market conditions. The configurator's pricing and product options cannot be hardcoded in React components. Changes to prices, setup scopes, or available connectors should not require a code deployment.

Values that change:

| Category | Example | Frequency |
|----------|---------|-----------|
| **Prices** | DGX Spark €5K → €6K, rental €500 → €550/Mo | Anytime (market-driven) |
| **Product options** | New connector type, dual DGX Spark added | Quarterly |
| **Setup scope** | Base days, full days, Manntage rates | Annually |
| **Campaign messaging** | Scarcity framing, disclaimer text | Per campaign |

The configurator reads current values from a data source at runtime. The PDF generation pipeline pulls the same values — configurator and PDF are always in sync.

#### Resolved: Payload CMS

Payload CMS v3 serves as content infrastructure for the entire website — not just the configurator. MIT-licensed, open-source, free to self-host. Figma acquired Payload in June 2025 and committed to continued open-source investment. The REICHERT project (Klimafolgenschutz website) already runs Payload v3 with PostgreSQL, Lexical rich text, and Resend email integration — a proven pattern within the team.

Payload v3 is Next.js-native — it embeds directly into the `/app` folder, sharing one codebase, one deployment. Server components access the database directly via Payload's Local API, no HTTP API calls needed. The admin panel runs at `/admin` on the same domain.

#### Cloudflare-Native Deployment

The entire stack runs on Cloudflare:

| Layer | Service | Purpose |
|-------|---------|---------|
| **Compute** | Cloudflare Workers | Next.js + embedded Payload v3 |
| **Database** | Cloudflare D1 | Serverless SQLite via `@payloadcms/db-sqlite` |
| **Media** | Cloudflare R2 | Product images, PDF assets, uploaded media |
| **Auth** | Cloudflare Access | Prospect email OTP, session management (see Part 1) |
| **Analytics** | Cloudflare Web Analytics | Baseline page-level metrics (free) |

No Supabase, no separate CMS server, no Docker. One deployment target. Payload on Cloudflare Workers is officially supported with one-click deployment.

#### Collection Model (Phased)

**MVP — PDF Quote System:**
- **Products** — Hardware configs and pricing (purchase/rental)
- **SetupScopes** — Base vs Full setup options with included connectors
- **DataConnectors** — Available connector types and pricing
- **Prospects** — Account management (email, company, expiry)
- **PDFAssets** — Branding elements, disclaimer text, comparison content
- **Media** — Image uploads (built-in Payload collection)

**V2 — Newsletter (3-6 months):**
- **Subscribers** — Email list with preferences and subscription status
- **Newsletters** — Rich text content, scheduling, send status

Email delivery via Resend — the same adapter used in the REICHERT project. The `payload-plugin-newsletter` community plugin provides subscriber management, scheduling, and pre-built signup forms. Marius's personal "Marketingstruktur" initiative — deferred but architecturally ready.

**V3 — Content Platform:**
- **BlogPosts** — Thought leadership and product marketing with Lexical rich text, draft/publish workflow
- **CaseStudies** — Client success stories (replaces the 5 static JSON files currently in the codebase)
- **Pages** — CMS-managed page sections (hero, CTAs, product descriptions currently hardcoded in React)
- **Legal** — Impressum, Datenschutz (same pattern as REICHERT)

**Globals (site-wide config):**
- **SiteConfig** — Company name, contact info, social links
- **Navigation** — Menu items, footer links
- **CampaignConfig** — Active campaigns, scarcity messaging, disclaimer text

The collection model is composable — MVP collections ship with the PDF quote system, V2 and V3 collections are added when the business need materializes. Adding a collection is a config change, not a rebuild.

### Part 7: Website Architecture Migration

The wilsch-ai.com website currently runs as a React 19 + Vite single-page application on Vercel, with Supabase authentication and all content hardcoded in React components or static JSON files. The Payload CMS integration requires migrating to Next.js — Payload v3 embeds natively into Next.js and cannot integrate with a Vite-based SPA.

| Aspect | Current | Target |
|--------|---------|--------|
| **Framework** | React 19 + Vite + React Router v7 | Next.js 15 + embedded Payload v3 |
| **Deployment** | Vercel | Cloudflare Workers + Pages |
| **Auth** | Supabase (magic link + password) | Cloudflare Access (email OTP) |
| **Content** | Hardcoded in components + 5 static JSON case studies | Payload CMS collections (D1 database) |
| **CMS** | None | Payload admin at `/admin` |
| **Blog/Newsletter** | None | V2/V3 collections when ready |

#### Why Rebuild Now

The site currently needs no server-side rendering — all routes are behind authentication, so SEO is irrelevant. Under normal circumstances, this would favor keeping the React + Vite stack and using Payload as a headless API.

However, the newsletter and blog capabilities are 3-6 months away, not years. When public content launches, SSR and SEO become essential. Rebuilding from Vite to Next.js later would require a second migration — paying migration tax twice. Doing it once during the PDF quote system implementation avoids this.

The existing codebase is manageable: ~960 lines in the main App.jsx, 9 routes, 5 case study JSON files, and standard libraries (Three.js, Framer Motion, shadcn/ui, Recharts) that all work in Next.js.

#### Migration Scope

| Component | Migration Path |
|-----------|---------------|
| **Routes** | React Router v7 → Next.js App Router (file-based) |
| **Auth** | Supabase `AuthContext` → Cloudflare Access middleware |
| **Content** | Hardcoded JSX → Payload CMS API / server components |
| **Case Studies** | Static JSON files → CaseStudies collection |
| **Styling** | Tailwind + shadcn/ui → unchanged (works in Next.js) |
| **3D/Animations** | Three.js + Framer Motion → client components (unchanged) |
| **Build** | Vite → Next.js build (`@cloudflare/next-on-pages`) |

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
- **Transcript (3. April):** [Grooming Session](https://app.fireflies.ai/view/01KN9MYRJHAEWB76FFPHASWZRT) — Cloudflare auth discussion, design doc review feedback
- **Session (Pass 3):** 0877d265-e31a-46e3-9b59-91010990d80c
- **Research (Pass 4):** [Payload CMS Docs](https://payloadcms.com/docs) · [REICHERT Repo](https://github.com/DaveX2001/06_ABTMAYR-REICHERT__klimafolgenschutz-website) · [Payload on Cloudflare](https://blog.cloudflare.com/payload-cms-workers/)
- **Session (Pass 4):** 8b1e37ff-7b58-4044-bfc0-54ed7aa7c3ce

🤖

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

