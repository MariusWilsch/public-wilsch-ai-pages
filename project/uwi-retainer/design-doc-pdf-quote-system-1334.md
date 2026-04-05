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

The cutover is phased: this auth layer runs alongside the existing Supabase auth gating wilsch-ai.com. Internal users keep their Supabase accounts during the transition — only new prospects go through Cloudflare Access for MVP. Full Supabase removal is deferred to a later migration pass.

This is also a validation experiment: testing how simple or hard Cloudflare Access is as an alternative to Supabase auth for sites that don't need a full project database. The strategic positioning: Supabase stays the auth layer for project applications (Invoice Agent, Call2Tanss, tax module), while Cloudflare Access becomes the pattern for one-off sites — similar to how Hippocampus v2 operates. If the experiment succeeds here, it becomes the default for future one-off deployments.

This decouples the website's authentication entirely from the shared Supabase project (WILSCH_AI_PROJECTS-PROD) that serves the project applications. Prospect accounts never enter the internal auth pool — the cross-application isolation concern that existed with Supabase auth disappears by architecture.

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

**Recommended: PostHog Cloud (EU region).** Managed product analytics with React SDK, per-user identification, page views, heatmaps, session replay, and funnel analysis. The free tier covers up to 1M events/month — prospect-scale traffic sits well under that ceiling. EU-hosted data residency satisfies DSGVO without self-hosting burden. Zero ops, zero hosting cost.

**Identity wiring:** When a prospect lands on any wilsch-ai.com page, a small client-side snippet fetches their Cloudflare Access identity from `/cdn-cgi/access/get-identity`, extracts the email claim, and calls `posthog.identify(email)`. Every event from that session is automatically tied to the prospect — no manual instrumentation per page.

**Baseline: Cloudflare Web Analytics** (free, zero-config). Privacy-first page-level analytics with no cookies and no JavaScript tag required. Provides aggregate page views, top pages, and referrer data out of the box. Cloudflare Web Analytics cannot answer "what did Hasloff view" — architecturally it has no concept of a named visitor (no cookies, no persistent identifier, aggregate-only). It complements PostHog for population-level questions but cannot replace it.

| Tool | Page-level | Per-user identity | Element-level | DSGVO |
|------|-----------|------------------|--------------|-------|
| **PostHog Cloud EU** (recommended) | ✅ | ✅ via identify() | ✅ Heatmaps + session replay | ✅ |
| **Cloudflare Web Analytics** (baseline) | ✅ aggregate only | ❌ architecturally impossible | ❌ | ✅ |

Tier 1 launches with both: Cloudflare Web Analytics for aggregate metrics, PostHog Cloud for per-user events via the identify() wire. Tier 2 activates PostHog heatmaps and session replay — the element-level capability Marius wants for configurator optimization.

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

Technical approach: HTML+CSS template → Puppeteer → PDF, rendered server-side in the CMS Next.js app. The configurator POSTs the prospect's selections to a CMS API route, which fetches branding assets and the template from Payload's Local API, merges them with the selections, and renders the PDF via `puppeteer-core` + `@sparticuz/chromium`. The same endpoint triggers the Tier 2 notification email (see Part 2). Markdown was considered but cannot express the multi-column layouts, per-page headers, and precise logo placement required for the Angebotsübersicht quality bar — HTML+CSS is the correct intermediate for branded business documents.

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

Payload v3 requires Next.js as its host framework — `@payloadcms/next` is the only official adapter. Payload embeds into a Next.js `/app` folder, with Server Components accessing the database directly via Payload's Local API (no HTTP round-trip), and the admin panel at `/admin` on the same domain. In the wilsch-ai.com architecture, this Next.js+Payload app lives as a **standalone service separate from the main Vite frontend** — see Part 7 for the split architecture details.

#### Docker Deployment on WILSCH-AI-SERVER

The CMS runs as a Docker Compose stack on WILSCH-AI-SERVER — the same infrastructure pattern as the REICHERT project (klimafolgenschutz-cms.wilsch-deployment.com). Proven, operational, no Cloudflare Workers learning curve, no D1 adapter bugs.

| Layer | Service | Purpose |
|-------|---------|---------|
| **Compute** | Docker Compose (Next.js 15 container) | Next.js + embedded Payload v3 |
| **Database** | PostgreSQL container via `@payloadcms/db-postgres` | Stable adapter, proven in REICHERT |
| **Media** | Local volume on WILSCH-AI-SERVER | Product images, PDF assets, uploaded media |
| **Auth (prospects)** | Cloudflare Access | Email OTP, session management (see Part 1) |
| **Auth (Payload admin)** | Payload's built-in auth | Separate login for Marius + David |
| **Analytics** | PostHog Cloud EU + Cloudflare Web Analytics | Per-user + aggregate (see Part 2) |

Hostname: `wilsch-ai-cms.wilsch-deployment.com` (follows REICHERT naming pattern). Deployment: git pull + `docker compose build && up -d`. Environment variables hold database credentials, Payload secret, Resend API key, and CORS origins (wilsch-ai.com).

Cloudflare Workers + D1 + R2 was considered and dropped. The D1 adapter has two open production bugs — SQL variable limits on large schemas and data-loss risk on array field updates without atomic batch operations. Paid Workers ($5/mo) is mandatory for the Payload template (3MB bundle size limit on free tier), and the team has no prior Workers experience. The REICHERT pattern ships all the same capabilities on familiar infrastructure with zero marginal hosting cost.

#### Collection Model (Phased)

**MVP — PDF Quote System:**
- **Products** — Hardware configs and pricing (purchase/rental)
- **SetupScopes** — Base vs Full setup options with included connectors
- **DataConnectors** — Available connector types and pricing
- **Prospects** — Account management (email, company, expiry)
- **PDFAssets** — Branding elements, disclaimer text, template configuration
- **ComparisonDimensions** — "Drei Wege zur KI" comparison (10 dimensions × 3 plans), consumed by both the Vite KI aus der Box page and the PDF Page 1 comparison context
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

### Part 7: Architecture — Split Apps

The wilsch-ai.com website currently runs as a React 19 + Vite single-page application on Vercel, with Supabase authentication gating all routes and content hardcoded in React components + 5 static JSON case studies. **No full rebuild.** The existing Vite site stays untouched — the PDF quote system is added via a companion CMS app.

| Layer | Location | Stack | Host |
|-------|----------|-------|------|
| **Public site + configurator** | `wilsch-ai.com` (unchanged) | React 19 + Vite + React Router v7 | Vercel |
| **CMS + PDF generation** | `wilsch-ai-cms.wilsch-deployment.com` (new) | Next.js 15 + Payload v3 + Postgres | WILSCH-AI-SERVER (Docker) |
| **Prospect auth gate** | Cloudflare Access (edge) | Email OTP | Cloudflare |

The Vite site fetches CMS content at runtime via Payload's REST API. The configurator calls the CMS's `/api/generate-quote` endpoint to produce PDFs server-side. CORS configured in `payload.config.ts` allows the wilsch-ai.com origin.

#### Why Split, Not Unified

REICHERT precedent: the Klimafolgenschutz website uses exactly this pattern — Vike/Vite frontend on Vercel, separate Next.js+Payload CMS on WILSCH-AI-SERVER, communicating via REST. It works operationally. Payload v3 requires Next.js (`@payloadcms/next` is the only official framework adapter), but Next.js does not have to be the frontend.

The design doc previously argued for a full Vite→Next.js rebuild to avoid "migration tax twice" when newsletter/blog public content ships in 3-6 months. That argument no longer holds. With split apps, future newsletter/blog capabilities can ship inside the Next.js CMS app directly — no migration of the existing Vite site is ever needed.

#### What Changes On The Vite Site (Minimal Touch)

The existing wilsch-ai.com codebase gets three targeted additions, not a rewrite:

| Touch | Scope |
|-------|-------|
| **Cloudflare Access gate** | Layered above existing Supabase auth (phased cutover, see Part 1). No code change in the Vite app — gate happens at the edge. |
| **Configurator component** | New React component added to the KI aus der Box page (or dedicated route). Fetches Products, SetupScopes, DataConnectors from CMS REST API. |
| **Drei Wege content migration** | `src/pages/KiAusDerBoxV2.jsx` lines 332-478: replace hardcoded `ComparisonSection` with a fetch from `CMS_URL/api/comparison-dimensions`. Single source of truth with the PDF Page 1 content. |

All other existing pages (App.jsx home, HardwareOptionen, KiPerformance, CaseStudy, etc.) remain hardcoded for MVP. Full CMS-driven content migration is a V2/V3 concern, not MVP scope.

#### Data Fetching Pattern

Following REICHERT's proven pattern — runtime REST calls with an env-var CMS URL:

```typescript
const CMS_URL = import.meta.env.PUBLIC_ENV__CMS_URL ||
                'https://wilsch-ai-cms.wilsch-deployment.com'

const products = await fetch(`${CMS_URL}/api/products`).then(r => r.json())
```

Types are generated on the CMS side via `payload generate:types` and manually copied to the Vite frontend — same sync pattern as REICHERT. A schema change in Payload triggers a type-sync commit in the Vite app.

#### PDF Generation

PDF rendering is a Next.js API route in the CMS app (see Part 4 for the technical pipeline). The Vite configurator POSTs selections to `CMS_URL/api/generate-quote`, the CMS renders with Puppeteer + Payload's Local API, returns the PDF buffer, and fires the notification email in the same request — one atomic operation.

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
- **Research (Pass 5):** [Cloudflare Web Analytics docs](https://developers.cloudflare.com/web-analytics/about/) · [PostHog identify() docs](https://posthog.com/docs/getting-started/identify-users) · [CF Access get-identity](https://developers.cloudflare.com/cloudflare-one/access-controls/applications/http-apps/authorization-cookie/application-token/) · [Payload D1 adapter issue #14766](https://github.com/payloadcms/payload/issues/14766) · [Payload D1 adapter issue #15219](https://github.com/payloadcms/payload/issues/15219) · REICHERT repo deep-dive (frontend Vike+Vite / `/cms` Next.js+Payload+Postgres on WILSCH-AI-SERVER) · wilsch-ai-site repo scan (7 pages, Drei Wege in KiAusDerBoxV2.jsx lines 332-478)
- **Session (Pass 5):** d1a3c2a0-6991-447f-afbf-55fa5eaeb697

🤖

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

