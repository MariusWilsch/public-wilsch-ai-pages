---
publish: true
---

# Design Doc: Klimaanpassungsmanager Recruitment Pipeline

[[client-abtmayr-reichert]]

Recruitment pipeline for Klimafolgenschutz e.V.: how the association connects municipalities with Werkstudenten as Klimaanpassungsmanager, using federal methodology frameworks.

---

## Problem Statement

Since July 2024, Germany's Klimaanpassungsgesetz (KAnG) requires every municipality to create a Klimaanpassungskonzept. The staffing gap is severe: only ~300 Klimaanpassungsmanager (KAM) are employed nationwide — compared to ~2,500 Klimaschutzmanager. The primary federal funding mechanism (DAS) has exhausted its budget since August 2025, with future funding windows planned but unscheduled.

Municipalities face a triple bind: legal obligation, no staff, limited funding. The professional KAM market cannot scale fast enough — formal positions require completed degrees and compete at TVöD EG 10–11 salary levels (~€3,500–4,500/month).

Klimafolgenschutz e.V. addresses this gap by creating a new position category: Werkstudenten as Klimaanpassungsmanager. The e.V. recruits students, provides the methodology framework (derived from PIK/UBA tools), and connects them with municipalities — supply and demand simultaneously.

> *"Wie bei Tesla — Autos und Ladestation gleichzeitig."* — Michael Reichert

**Preconditions:**
- KAnG is in force — municipalities have a legal obligation to act
- DAS funding window closed (Aug 2025), Förderrichtlinie valid until Dec 2026
- PIK/UBA provide free methodology tools (Klimalotse 3.0, GERICS, Anpassungsscanner)
- Klimafolgenschutz e.V. is operational (website live, bank account, Vereinsregister)
- No "KAM-Werkstudent" position type exists in the current market

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Michael can recruit Klimaanpassungsmanager (Werkstudenten) for municipalities through a unified pipeline — from job description to LinkedIn presence to website integration |
| **Success** | A published design doc that Marius can decompose into developer sub-issues covering all 4 deliverables, with enough specificity that implementation doesn't require another design session |
| **Done test** | Can I write a meeting agenda with open design questions? If NO → design is complete |

---

## Foundation: Institutional & Terminology Reference

The recruitment pipeline operates within Germany's federal climate adaptation ecosystem. This section grounds the institutions, legal frameworks, methodology tools, and professional terminology referenced throughout the Approach.

### Institutional Landscape

| Institution | Full Name | Role | Relevance |
|-------------|-----------|------|-----------|
| **UBA** | Umweltbundesamt (Federal Environment Agency) | Builds methodology tools (Klimalotse, Anpassungsscanner, Tatenbank) | Source of the operational framework students use |
| **PIK** | Potsdam-Institut für Klimafolgenforschung | Produces climate science and projections | State-funded, content is publicly licensed — reusable 1:1 for the Agenda |
| **GERICS** | Climate Service Center Germany (Helmholtz) | Translates science into municipality-specific climate reports (Klimaausblicke) | One PDF per Landkreis with local projections — the student's starting data |
| **ZKA** | Zentrum KlimaAnpassung (run by adelphi for BMUKN) | Supports hired KAMs: training (2.5-day seminars, 6x/year), mentoring, 100-day onboarding guide | Free for funded KAMs; Werkstudent eligibility untested |
| **ZUG** | Zukunft – Umwelt – Gesellschaft | Distributes federal funding (DAS for adaptation, NKI for mitigation) | Project carrier — administers the money, not the methodology |
| **KLiVO** | Deutsches Klimavorsorgeportal | Government meta-portal indexing all federal adaptation tools | Search engine, not a tool — finds Klimalotse, Anpassungsscanner, Tatenbank in one place |

### Legal & Funding Framework

| Term | Definition |
|------|-----------|
| **KAnG** (Klimaanpassungsgesetz) | Federal law (Nov 2023) requiring every municipality to create a Klimaanpassungskonzept. States must submit adaptation strategies by Jan 31, 2027. No explicit penalties — legal obligation without enforcement mechanism, but creates political pressure and budget justification. |
| **DAS** (Deutsche Anpassungsstrategie) | Federal grant program funding KAM positions and adaptation concepts. Budget exhausted since Aug 2025; Förderrichtlinie valid until Dec 2026. "Valid but exhausted" = rules exist, new funding windows planned but unscheduled. |
| **NKI** (Nationale Klimaschutzinitiative) | Parallel program for climate *protection* (mitigation), not adaptation. Funds Klimaschutzmanager positions. Same carrier (ZUG), different purpose. |
| **TVöD EG 10–11** | Public sector pay scale for professional KAMs: ~€3,500–4,500/month gross. Relevant as positioning benchmark — Werkstudenten cost a fraction. |
| **Klimaanpassungskonzept** | The document municipalities must produce per KAnG: a formal climate risk assessment, adaptation strategy, and implementation plan. |
| **Ratsbeschluss** | Municipal council resolution — the political adoption of the Klimaanpassungsstrategie. Required to formalize the strategy. |

### Methodology Terms

| Term | Definition |
|------|-----------|
| **Klimalotse 3.0** | UBA's step-by-step adaptation guide (website + downloadable PDF). Five sequential modules with templates and checklists. Not a calculator — a structured workflow that produces the basis for a Klimaanpassungskonzept. |
| **Wirkungsketten** (Impact Chains) | Causal chains mapping climate driver → exposure → sensitivity → impact. Example: *Rising temperatures → Heat waves → Urban surfaces radiate heat → Increased heat mortality.* Used in Module 2 to systematically identify which sectors are affected. |
| **Anpassungsscanner** | UBA Excel self-assessment measuring institutional readiness (not physical risk). Five dimensions: (1) Values & goals, (2) Knowledge access, (3) Collaboration, (4) Resources, (5) Strategies. Outputs a 0–100 score. |
| **GERICS Klimaausblicke** | Auto-generated PDFs with climate projections per Landkreis across three RCP scenarios. Indicators include: annual mean temperature, hot days (≥30°C), tropical nights, frost days, heavy precipitation, drought periods, snow cover. |
| **RCP Scenarios** | Standardized emissions trajectories: RCP 2.6 (strong mitigation, ~+1.5–2°C), RCP 4.5 (moderate, ~+2–3°C), RCP 8.5 (business as usual, ~+3.5–5°C). Used in all GERICS projections. |
| **Tatenbank** | UBA database of 432 real-world adaptation projects. Good-practice library searchable by action area, climate impact, and region. Students use it in Module 3 to find proven measures. |
| **DIN EN ISO 14091** | International standard for climate risk assessment (2021). Best practice, not mandatory — but increasingly referenced in formal concepts and procurement specs. |
| **Schwammstadt** (Sponge City) | Urban planning concept: absorb, store, and slowly release rainwater instead of piping to sewers. Concrete elements: permeable pavements, green roofs, bioswales, urban trees, de-sealing. |
| **Maßnahmensteckbriefe** | Standardized measure profiles documenting individual adaptation measures — what, where, cost, timeline. Students draft these in Module 3. |
| **Fördermittelantragstellung** | Funding application process. Students research programs and prepare applications; the municipality signs and submits. |

### Professional Landscape

| Term | Definition |
|------|-----------|
| **Klimaschutzmanager** (~2,500 nationwide) | Municipal role focused on climate *protection* — reducing CO₂ emissions (solar, retrofitting, mobility). Funded by NKI. |
| **Klimaanpassungsmanager** (~300 nationwide) | Municipal role focused on climate *adaptation* — preparing for impacts already occurring (heat, flooding, drought). Funded by DAS. The newer, undersupplied role. |
| **KAM-Werkstudent** | The position type this pipeline creates. Does not exist in the current market — lower barrier than professional KAM (student, not degree required), with thesis integration and structured methodology via Agenda. |

---

## Approach

The pipeline has four parts, each building on the previous:

```
PIK/UBA Research ──→ Agenda ──→ Stellenbeschreibung ──→ LinkedIn ──→ Website CTA
```

### Part 1: Klimaanpassungsmanager Agenda

A dual-purpose document: it tells candidates what they'll do when deployed, and it gives municipalities confidence in the methodology. Derived from the federal Klimalotse 3.0 framework (UBA), not invented from scratch.

**Structure: Klimalotse 5-Module Framework**

The agenda presents the Klimalotse lifecycle adapted for the Werkstudent scope. Each module describes the overall municipal process and what the student specifically contributes:

| Module | Municipal Process | Student's Contribution |
|--------|------------------|----------------------|
| **1. Orientierung** | Secure political mandate, appoint KAM, establish governance | Map stakeholder landscape, research past weather events, compile GERICS climate projections, run UBA Anpassungsscanner |
| **2. Klimarisikoanalyse** | Formal risk assessment per DIN EN ISO 14091, stakeholder workshops | Develop Wirkungsketten (impact chains), gather sensitivity/exposure data, pre-fill risk matrices, document workshop results |
| **3. Maßnahmenentwicklung** | Define adaptation goals, select and prioritize measures | Review UBA Tatenbank, draft Maßnahmensteckbriefe, research funding programs, support applications |
| **4. Strategiedokument** | Publish Klimaanpassungsstrategie, secure Ratsbeschluss | Editorial support, build monitoring framework, coordinate first measures |
| **5. Monitoring** | Track implementation, evaluate outcomes | Maintain indicator sets, prepare progress reports |

**Priority areas** (Michael's emphasis): Gewässerschutz, Hitzeschutz, Bepflanzung, Innenstadtgestaltung — these are typical municipal needs, not a narrowing of the framework.

**Tools the student uses:**

| Tool | Purpose | Access |
|------|---------|--------|
| [Klimalotse 3.0](https://www.umweltbundesamt.de/node/33047) | Step-by-step adaptation methodology | Free, downloadable |
| [GERICS Klimaausblicke](https://www.gerics.de/products_and_publications/fact_sheets/landkreise/index.php.de) | 17 climate indicators per Landkreis, 3 RCP scenarios | Free, 401 Landkreise |
| [UBA Anpassungsscanner](https://www.umweltbundesamt.de/node/118272) | Excel self-assessment across 5 dimensions | Free (since Sep 2025) |
| [KLiVO Portal](https://www.klivoportal.de) | Meta-tool aggregating all federal adaptation tools | Free |
| [ZKA 100-Tage Guide](https://zentrum-klimaanpassung.de/wissen-klimaanpassung/beruf-klimaanpassungsmanagerin/die-ersten-100-tage-im-klimaanpassungsmanagement) | Onboarding playbook for new KAMs | Free, public download |

**What students do NOT do:** Political mandate negotiation, leading stakeholder workshops (they support and document), final risk assessment decisions, Fördermittelantragstellung (students prepare, municipality signs).

**Support infrastructure:** ZKA offers free training seminars (2.5 days, 6x/year), a mentoring program (peer matching), and a digital platform for KAMs. Werkstudent access to these programs is not explicitly excluded but untested — registration forms include "Sonstiges" and "Stellenumfang in %" fields.

### Part 2: Stellenbeschreibung

German job description for the Werkstudent position. Content derived from the Agenda — the Stellenbeschreibung is the Agenda translated into a recruitment format.

**Employment model:**

| Role | Actor |
|------|-------|
| **Employer** | Municipality (not the e.V.) |
| **Recruiter** | Klimafolgenschutz e.V. — sources and connects candidates |
| **Methodology** | The Agenda (Part 1) guides the student's work |
| **Funding** | **Undefined:** DAS eligibility for Werkstudenten is untested — see [Meeting Agenda, Topic 1](#1-das-funding-eligibility-for-werkstudent-kam-positions) |

**Target candidates:**

| Attribute | Detail |
|-----------|--------|
| **Type** | Werkstudent (max. 20h/week during semester) |
| **Faculties** | Tiefbauingenieurwesen, Wasserwirtschaft, Geographie, Umweltwissenschaften, BWL/Ökonomie |
| **Location** | On-site at municipality — *"der läuft praktisch in der Gemeinde rum"* |
| **Scope** | Bundesweit — no geographic restriction |
| **Thesis** | Optional — students may write their Bachelorarbeit on the deployment topic |

**Stellenbeschreibung content (draft structure):**

- **Title:** Werkstudent/in Klimaanpassungsmanagement (m/w/d)
- **Über Klimafolgenschutz e.V.:** Association mission, municipal connection
- **Deine Aufgaben:** Derived from Agenda modules 1–4
- **Dein Profil:** Faculty requirements, interest in Klimaanpassung, structured working style, German fluency, GIS von Vorteil
- **Wir bieten:** Praxiserfahrung, Bachelorarbeit possibility, ZKA network access, flexible hours, direct municipal impact

**Market positioning:** No "KAM-Werkstudent" exists in the current job market. This differentiates from professional KAM postings by: lower barrier (student, not degree required), thesis integration, structured methodology via Agenda, and ZKA network access through the e.V. connection.

### Part 3: LinkedIn Company Page + Content Strategy

New organizational LinkedIn page for Klimafolgenschutz e.V. — Michael has a personal account only. The page serves two audiences simultaneously (Tesla model): students looking for opportunities and municipalities looking for staffing solutions.

**Page setup:**

| Attribute | Value |
|-----------|-------|
| **Page name** | Klimafolgenschutz e.V. |
| **Category** | Nonprofit Organization |
| **Tagline** | *Gemeinden stärken. Klima anpassen. Zukunft sichern.* |
| **Website** | klimafolgenschutz.com |
| **Email** | info@klimafolgenschutz.org |
| **Admin** | Marius creates page, Michael gets admin access |

**Content pillars:**

| Pillar | Share | Audience | Example |
|--------|-------|----------|---------|
| **Klima-Wissen** | ~30% | Both | GERICS Klimaausblick highlights, Schwammstadt explainer |
| **Karriere** | ~30% | Students | "Was macht ein/e KAM?", Werkstudent opportunities |
| **Gemeinden** | ~25% | Municipalities | KAnG obligations, DAS funding updates, success stories |
| **Verein** | ~15% | Both | e.V. milestones, PIK/UBA contact updates |

**Posting cadence:** 1–2x per week (steady state).

**Content approval:** **Undefined:** Does Michael review posts before publishing? — see [Meeting Agenda, Topic 2](#2-linkedin-content-approval-workflow)

**Tone:** Professional but accessible — government-adjacent credibility with student-friendly language. German only (DACH focus).

### Part 4: Website CTA Integration

Changes to the existing Klimaanpassungsmanager section on klimafolgenschutz.com. This is the simplest deliverable — a rename, a link swap, and badge removal.

**Changes:**

| Element | Current | New |
|---------|---------|-----|
| **Section title** | Klimafolgenschutzmanager | **Klimaanpassungsmanager** |
| **CTA button** | Interesse bekunden → mailto:info@klimafolgenschutz.org | Interesse bekunden → **LinkedIn job posting URL** |
| **Badge** | "Bald verfügbar" | **Remove** |
| **Subtext** | "im Aufbau" | **Remove** |

**Rename scope:** Site-wide — every instance of "Klimafolgenschutzmanager" becomes "Klimaanpassungsmanager." Michael's rationale: *"Vielleicht benennen wir das um den Klimaanpassungsmanager, weil das die Begrifflichkeit ist, die die Behörde schon eingeführt hat."*

**Dependency:** Part 3 (LinkedIn page + job posting) must be live before the CTA can point to it.

```
Part 3 (LinkedIn page live) ──→ Part 4 (swap CTA URL)
```

**Deferred items:**

| Item | Reason | When |
|------|--------|------|
| **/wissen page** | Michael: *"kann sich ja entwickeln noch"* — no concrete scope | After Agenda content matures |
| **Zertifizierung** | Michael: *"warten wir damit ab bis zum Schluss"* | After program runs with first deployments |

---

## Source

- **Transcripts:** [Fireflies 2026-03-23](https://app.fireflies.ai/view/01KMDCMMAF48SP1FBQ0JWW51R7) — Primary requirements (Michael, Marius, David)
- **Email:** [PIK Links from Michael](https://mail.google.com/mail/u/0/#all/19d1ae8a29c64a84) — UBA Kompass + GERICS
- **Artifacts:**
  - [DAS-Förderrichtlinie 2021 (PDF)](https://www.z-u-g.org/fileadmin/zug/Dateien/Foerderprorgamme/DAS_Anpassung_an_den_Klimawande/25-01-DAS-Foerderrichtlinie_2021_ab_06.11.24_bf.pdf) — Federal KAM funding rules
  - [KAnG (Bundestag)](https://www.bundestag.de/dokumente/textarchiv/2023/kw46-de-bundesklimaanpassungsgesetz-976584) — Municipal legal obligation
  - [UBA Klimalotse 3.0](https://www.umweltbundesamt.de/themen/klimalotse-30-aktualisierter-leitfaden-zur) — 5-module methodology
  - [GERICS Klimaausblicke](https://www.gerics.de/products_and_publications/fact_sheets/landkreise/index.php.de) — 17 indicators per Landkreis
  - [UBA Anpassungsscanner](https://www.umweltbundesamt.de/node/118272) — Excel self-assessment
  - [KLiVO Portal](https://www.klivoportal.de) — Government tool aggregator
  - [ZKA KAM Profession](https://zentrum-klimaanpassung.de/wissen/beruf-klimaanpassungsmanagerin/beruf-klimaanpassungsmanagerin) — Role definition
  - [NKI / DAS Funding](https://www.z-u-g.org/das/) — Federal program overview
  - [NKI Municipal Agency](https://www.klimaschutz.de/de/foerderung-der-nki/ueber-die-nki-dashboard/agentur-fuer-kommunalen-klimaschutz) — Advisory for municipalities
- **Sessions:**
  - [Rubber-duck #1317](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-verdant-Documents-projects-billable-klimafolgenschutz-website/44abeabd-6d82-4c29-bb50-72c46a609995.jsonl) — Consolidation rationale (#1242 + #1243 → #1317)
  - [JA Extraction #1319](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-06-ABTMAYR-REICHERT--deliverable/75a1bfd3-0f96-431d-93d5-61121cf5ef2e.jsonl) — Initial design doc session
  - [JA Terminology Pass #1319](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-06-ABTMAYR-REICHERT--deliverable/105fe43b-af01-4fcf-bc33-d701510b5f4e.jsonl) — Foundation glossary + client-facing cleanup

*Supersedes: #1317 (closed), #1242 (LinkedIn Content Strategy), #1243 (PIK Research Integration) — all closed as "not planned."*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

