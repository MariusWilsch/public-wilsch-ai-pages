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
| **Success** | A published design doc that Marius can decompose into developer sub-issues covering all 5 pipeline parts, with enough specificity that implementation doesn't require another design session |
| **Done test** | Can I write a meeting agenda with open design questions? If NO → design is complete |

---

## Team

| Role | Person | Responsibility |
|------|--------|---------------|
| **Ambassador** | Michael Reichert | Vision, PIK/UBA network, municipal contacts, funding research |
| **Geschäftsführer (designiert)** | Ralph Loser | Operations, project planning, organizational structure |
| **Technical Lead** | Marius Wilsch / Wilsch AI | Platform development, content production, AI automation |
| **Technical Support** | David Eberle | Implementation support |

Ralph joined the project via V-Con network (March 2026). Weekly team meetings established. Detailed project plan — milestones, responsibilities, September 2026 target — to be created at brainstorming session (2026-04-03).

---

## Approach

The pipeline has five parts. Parts 1–4 build the student recruitment side. Part 5 runs in parallel — the municipal acquisition side. Both converge at the platform (Part 4).

```
PIK/UBA Research ──→ Agenda ──→ Stellenbeschreibung ──→ LinkedIn ──→ Platform + Automation
                                                                          ↕
                                                               Municipal Acquisition (Part 5)
```

### Part 1: Klimaanpassungsmanager Agenda

An internal document given to accepted applicants after they pass the application process. It defines their work scope — what they'll do when deployed to a municipality. Derived from the federal Klimalotse 3.0 framework (UBA), not invented from scratch. Municipalities receive separate product-focused materials (see Part 5).

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

**Employment paths (case-by-case):**

| Path | Hours | Compensation | Use case |
|------|-------|-------------|----------|
| **Werkstudent** | max. 20h/week (semester) | Hourly, above Minijob | Primary track — longer engagements, Bachelorarbeit |
| **Übungsleiter** | 10–12h/week | 400–500€/month (pauschal) | Lighter engagement, entry-level |
| **Pflichtpraktikum** | Per university requirement | Per arrangement | Students with mandatory internship requirements |

The employment path is determined case-by-case during the matching process. One Stellenbeschreibung covers all three paths.

**Target candidates:**

| Attribute | Detail |
|-----------|--------|
| **Type** | Werkstudent, Übungsleiter, or Pflichtpraktikant (see employment paths above) |
| **Semester** | Ab dem 2. Semester — targeting students early enough for Bachelorarbeit integration |
| **Faculties** | Tiefbauingenieurwesen, Wasserwirtschaft, Geographie, Umweltwissenschaften, BWL/Ökonomie |
| **Location** | On-site at municipality — *"der läuft praktisch in der Gemeinde rum"* |
| **Scope** | Bundesweit — no geographic restriction |
| **Thesis** | Optional — students may write their Bachelorarbeit on the deployment topic |

**Stellenbeschreibung content (draft structure):**

- **Title:** Werkstudent/in Klimaanpassungsmanagement (m/w/d)
- **Über Klimafolgenschutz e.V.:** Association mission, municipal connection
- **Deine Aufgaben:** Derived from Agenda modules 1–4
- **Dein Profil:** Faculty requirements, interest in Klimaanpassung, structured working style, German fluency, GIS von Vorteil
- **Wir bieten:** Praxiserfahrung, Bachelorarbeit possibility, Pflichtpraktikum-Anrechnung, ZKA network access, flexible hours (Werkstudent or Übungsleiter), direct municipal impact

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
| **Additional admin** | Ralph Loser (Geschäftsführer) — access level TBD |

**Content pillars:**

| Pillar | Share | Audience | Example |
|--------|-------|----------|---------|
| **Klima-Wissen** | ~30% | Both | GERICS Klimaausblick highlights, Schwammstadt explainer |
| **Karriere** | ~30% | Students | "Was macht ein/e KAM?", Werkstudent opportunities |
| **Gemeinden** | ~25% | Municipalities | KAnG obligations, DAS funding updates, success stories |
| **Verein** | ~15% | Both | e.V. milestones, PIK/UBA contact updates |

**Posting cadence:** 1–2x per week (steady state). The existing doc proposed daily — too aggressive for current bandwidth.

**Content production:** Marius/team produces content. Michael confirmed: *"Kannst du das mit übernehmen?"* — *"Ja, können wir machen."*

**Content approval:** **Undefined:** Does Michael review posts before publishing? Ralph's role in content review is also undefined. — see [Meeting Agenda, Topic 2](#2-linkedin-content-approval-workflow)

**Tone:** Professional but accessible — government-adjacent credibility with student-friendly language. German only (DACH focus).

### Part 4: Platform + Automation

The existing Klimaanpassungsmanager section on klimafolgenschutz.com transforms from a static interest form into an automated recruitment and matching platform. This happens in two phases: immediate CTA changes (current), then full platform build (target).

**Phase 1: Website CTA changes (immediate)**

| Element | Current | New |
|---------|---------|-----|
| **Section title** | Klimafolgenschutzmanager | **Klimaanpassungsmanager** |
| **CTA button** | Interesse bekunden → mailto:info@klimafolgenschutz.org | Interesse bekunden → **LinkedIn job posting URL** |
| **Badge** | "Bald verfügbar" | **Remove** |
| **Subtext** | "im Aufbau" | **Remove** |

**Rename scope:** Site-wide — every instance of "Klimafolgenschutzmanager" becomes "Klimaanpassungsmanager." The term aligns with the official federal designation used by UBA and ZKA.

**Dependency:** Part 3 (LinkedIn page + job posting) must be live before the CTA can point to it.

```
Part 3 (LinkedIn page live) ──→ Part 4 Phase 1 (swap CTA URL) ──→ Phase 2 (platform)
```

**Phase 2: Automated application pipeline (target)**

A fully KI-driven recruitment and matching system where no personal interviews or phone calls are required.

**Student-side flow:**
1. Student finds opportunity (LinkedIn, university, website)
2. Clicks through to application page on klimafolgenschutz.com
3. Uploads: Studentenausweis, Studienverlauf, qualifications, motivation
4. KI validates eligibility (semester, faculty, location)
5. System matches with nearby municipalities (radius-based)
6. Accepted applicant receives Agenda (Part 1) + onboarding materials

**Municipality-side flow:**
1. Municipality registers need for KAM support (see Part 5)
2. System matches available students within commutable radius
3. Automated assignment or self-selection by student

**Key design principle:** The platform replaces manual coordination at scale — no personal calls, no interview scheduling, no manual matching. KI handles validation and assignment so the team focuses on pipeline growth, not case-by-case management.

**Undefined:** Specific KI validation rules, matching algorithm, radius parameters, and rejection handling — these require data from initial applicants before specification. See Meeting Agenda.

**Deferred items:**

| Item | Reason | When |
|------|--------|------|
| **/wissen page** | No concrete scope yet | After Agenda content matures |
| **Zertifizierung** | Wait until program runs | After first deployments |

### Part 5: Municipal Acquisition + Product Packaging

The demand side of the pipeline — running in parallel with student recruitment (Parts 1–4). Municipalities are the paying customers; students are the deployed workforce. Both sides must grow simultaneously.

**Acquisition approach:**

Municipalities face a legal obligation (KAnG) but lack staff. The e.V. positions itself as the staffing solution: pre-trained Werkstudenten deployed with a proven methodology (Klimalotse). The value proposition is concrete — not consulting, but actual capacity.

**Product packaging:**

The website offers municipalities concrete "products" — packaged adaptation measures with scope, cost, and funding information. Think project tiles (like the existing E-Bike Ladestation on the website), but for climate adaptation.

**Undefined:** The specific products depend on data Michael is requesting from his Berlin contact (PIK/UBA) — the three most frequently funded adaptation measures nationwide. Candidates include:

- Gewässerschutz (water protection)
- Hitzeschutz (heat protection)
- Rückhaltebecken-Vergrößerung (retention basin expansion)
- Ausgleichsflächen (compensation areas)
- Bepflanzung / Innenstadtgestaltung (greening / urban design)

Each product would include: measure description, typical municipal scope (Gemeindegröße), required disciplines (Baufirmen, Architekten), estimated effort, and applicable Förderquote by Bundesland.

**Undefined:** Product definitions — waiting on Michael's Berlin contact for top 3 funded measures. See [Meeting Agenda, Topic 3](#3-municipal-product-packaging).

**Municipality registration flow:**

Municipalities register their interest through the website (see Part 4 Phase 2, municipality-side flow). The system captures: Gemeindegröße, location, priority areas, existing Klimaanpassungskonzept status.

**Undefined:** Municipality application form fields, qualification criteria, and onboarding process. These will be defined after the first product packages are specified.

---

## Source

- **Transcripts:**
  - [Fireflies 2026-03-23](https://app.fireflies.ai/view/01KMDCMMAF48SP1FBQ0JWW51R7) — Primary requirements (Michael, Marius, David)
  - [Fireflies 2026-03-30](https://app.fireflies.ai/view/01KMZ8E02X2RGB29M3DSFQJVP1) — Client sync with Ralph Loser, Michael Reichert (team structure, municipal products, employment paths, automation vision)
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
  - [JA Extraction #1319](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-06-ABTMAYR-REICHERT--deliverable/75a1bfd3-0f96-431d-93d5-61121cf5ef2e.jsonl) — Initial extraction pass
  - [JA Extraction #1319 Pass 2](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-06-ABTMAYR-REICHERT--deliverable/d6ef71e6-291d-4d98-81d5-f21864cf0bc3.jsonl) — Meeting integration pass (2026-03-30 transcript)

*Supersedes: #1317 (closed), #1242 (LinkedIn Content Strategy), #1243 (PIK Research Integration) — all closed as "not planned."*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

