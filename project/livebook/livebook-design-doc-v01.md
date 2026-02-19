---
publish: true
---

# Livebook — Design Doc v0.1 (Tipping Point)
[[livebook]]

First extraction pass: initial alignment document for the Livebook platform. This is a conversation trigger for the first alignment meeting with Stafford — not a finished specification. Many sections are intentionally marked Undefined to drive discussion.


---

## Problem Statement

People's life stories go unpreserved. When someone dies, decades of experiences, achievements, and wisdom disappear with them. Children inherit a fraction of their parents' lives — the 40-50 years before they were born remain unknown unless someone deliberately captures them.

No existing tool makes story capture feel like a genuine conversation. Forms feel like chores. Questionnaires feel clinical. Writing it down requires a patience most people don't have. But AI does — AI has the absolute patience to sit, listen, and meet someone at their level for as long as it takes. AI can be relatable, adapt its modality (text or voice), and never rush. The right trigger at the right moment, paired with a listener who never tires, is what unlocks genuine stories.

**Preconditions:**
- People have stories worth preserving but lack the mechanism to capture them naturally
- Current tools (journals, memoir services, genealogy platforms) require effort that feels like work, not conversation
- A relatable trigger is needed — people won't start unprompted
- The capture experience must feel like two people going back and forth, not a data collection exercise

**Reference:** [Anthropic Interviewer](https://www.anthropic.com/research/anthropic-interviewer) — AI-powered interviews at scale, demonstrating that people open up to AI interviewers with 97.6% satisfaction when the experience feels like genuine conversation.

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A platform that turns lived experience into lasting books — through genuine AI conversation that feels like talking to a like-minded friend, not a clinical interviewer. The book becomes a person's legacy: something their grandchildren can read, something they're proud of. |
| **Success** | A user opens the platform, sees their empty book visualized in front of their eyes — not a vague idea in their head, but a tangible artifact with empty pages. Something relatable from their day triggers them to talk. The AI meets them at their level, like a friend who genuinely cares. Their book becomes less empty. They come back because the book is never done. |
| **Done test** | "Can I write a meeting agenda with open design questions?" → **YES.** This is v0.1 — a tipping point to ground the first alignment meeting. Multiple Undefined sections remain by design. |

---

## Approach

### Part 1: Product Identity & Audience

**Product:** Livebook (working title). A platform that produces lasting life story books through AI-powered conversation. **Undefined:** Product name — "EasyBook" is current branding but under review. → [Meeting Agenda Topic 1](#1-product-naming)

**Core value proposition:**
- "You drop dead tomorrow. This is your book. Would you be happy?"
- *or:* "Your book is empty. Fill it."

**Audience segments (initial, including but not limited to):**

| Segment | Hook | Why it works |
|---------|------|-------------|
| **Young adults** | Aspirational — start building your legacy now | Pride in achievements, desire to be remembered |
| **Elderly** | Preservation — capture what your grandchildren will never know | 40-50 years of life that children never witnessed |
| **Children of aging parents** | Gift — give your parent a way to share their story | The sister-bought-dad-a-book pattern |

**V1 beachhead:** Network School (NS) community — built-in audience, immediate feedback loop. NS Family Book is a pre-existing concierge MVP (no-tech, Stafford-led) that validates demand before the platform exists.

**Undefined:** Community books (Ladies Lounge, My Hometown) relationship to V1 platform scope — are they out of scope or test cases? → [Meeting Agenda Topic 2](#2-community-books-and-v1-scope)

---

### Part 2: Agent Architecture — Plug → Opera → Shakespeare

Three single-responsibility agents form the core pipeline. Each agent has one job and does it extremely well.

```
Plug ──→ Opera ──→ Shakespeare ──→ Livebook
(context)  (interview)  (book output)    (artifact)
```

![Livebook Pipeline — Plug → Opera → Shakespeare](assets/livebook-pipeline.png)

**Plug — Context Collector**
Creates relatable triggers from real life. Location, calendar, daily events — whatever makes the user go "oh yeah, that's true." The purpose is to activate neural memory chains: one relatable observation triggers associated memories, which trigger deeper stories. Like a sales process — three relatable "yeses" and the person is in a different headspace.

Plug does not interview. Plug collects trigger material — async or synchronous — so Opera has something genuine to start with.

**Opera — Smart Interviewer**
Takes Plug's relatable triggers and conducts adaptive interviews. The interaction pattern follows a tested knowledge extraction methodology:

1. **Planning** — understand the person's context (what Plug provides)
2. **Adaptive probing** — one reliable element at a time, following association chains. Never present a questionnaire. Feel like a like-minded friend.
3. **Structured output** — interview responses organized for Shakespeare

Opera's design principles:
- **Same level** — peer-like, not authoritative
- **One element at a time** — never overwhelm, never show too much
- **Genuine conversation** — two people going back and forth
- **Trigger, don't interrogate** — people love talking about themselves
- **Modality-agnostic** — voice or text is a surface layer; the interaction pattern is the design

**Undefined:** Voice cloning as a modality for Opera — open-source TTS models (350M params, 26 languages) make this technically feasible. Scope for V1 TBD. → [Meeting Agenda Topic 3](#3-voice-cloning-v1-scope)

**Shakespeare — Book Producer**
Takes Opera's structured output and transforms it into formatted book pages. Decides HOW stories become book content — layout, narrative flow, visual design.

**Undefined:** Shakespeare's output format — physical A5 book, digital, or both. Fulfillment pipeline (printing, shipping) entirely undefined. → [Meeting Agenda Topic 4](#4-book-output-and-fulfillment)

**Reference:**
- [Anthropic Interviewer](https://www.anthropic.com/research/anthropic-interviewer) — AI interviews at scale (1,250 professionals, 97.6% satisfaction)

---

### Part 3: Revenue Model Principle

**"Extraction is the value. Creation is free."**

| Pipeline Stage | Cost | Rationale |
|---------------|------|-----------|
| **Plug** (triggers) | Free | Hooks them in, creates daily engagement habit |
| **Opera** (interview) | Free | Builds emotional investment through genuine conversation. The interview IS the sales funnel — people invest their stories. |
| **Shakespeare** (book output) | Paid | Extraction crystallizes value — turning conversation into a tangible artifact |

The book is never done. Life keeps happening. Plug keeps triggering. Opera keeps interviewing. Shakespeare keeps producing new chapters. **Continuous extraction = subscription model.** Users don't pay for access — they pay for their book growing.

**Undefined:** Pricing specifics — subscription amount, one-time book purchase, tiered model. → [Meeting Agenda Topic 5](#5-revenue-model-and-pricing)

---

### Part 4: Delivery & Engagement

Marius takes on delivery of the product — accountable for turning vision into working software. The engagement starts with product delivery and has the potential to expand into broader company systems over time.

**Comp model direction:** Base retainer + success-based component. Details to be finalized based on V1 scope and timeline alignment.

**Undefined:** Engagement specifics — scope, terms, team integration. → [Meeting Agenda Topics 6-7](#6-team-structure-and-integration)

---

### Part 5: Methodology Foundation

Opera's interview methodology is adapted from a tested knowledge extraction protocol used in AI-assisted design work — proven across multiple client projects to produce genuine, structured output from conversational input.

The core principles (same-level interaction, one element at a time, relatable triggers, adaptive probing) transfer from professional knowledge extraction to personal story extraction. The mechanism is identical; the domain and emotional tone differ.

**Reference:** [Junior Architect Protocol](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-position-agreement-wilsch-ai-services)

---

### Part 6: Technical Architecture

Implementation-specific decisions are premature at this stage. The following are noted as areas requiring definition once V1 feature scope is locked:

- **Hosting & infrastructure** — TBD based on V1 feature set
- **Multi-language TTS** — TBD based on audience scope
- **Social media integrations** — observed in existing mockups (Facebook, Instagram, LinkedIn, Discord import), scope to be confirmed

**Undefined:** All technical architecture decisions depend on V1 feature scope. → [Meeting Agenda Topic 8](#8-v1-feature-scope-and-technical-requirements)

---

## Source

**Transcripts:**
- [hmf-nphz-oeh](https://app.fireflies.ai/view/01KHRCZ9PFND0HC5Z4R5W1ZH5B) — 50 min, Feb 18 2026 — Product vision deep-dive with Stafford (lifebook app, AI voice, NFTs, storytelling platform, legacy preservation)
- [hhb-sesi-hsp](https://app.fireflies.ai/view/01KHRGHPQTBECRD3JRHTTGTQ7Y) — 14 min, Feb 18 2026 — Business relationship, VP delivery role, E-Myth, collaboration planning

**Data Artifacts:**
- `My_NS_Family_Book_Original_Drawings_and_UX_Plan.docx` — Original UX flow, design system, book templates
- `My_NS_Family_Book_Updated_UX_Project_Reset.docx` — Updated UX with resolved feedback
- 7x WhatsApp mockup images — Pick Your Story, Landing, Social Linking, Permissions, Profile, Sign In, Book Confirmation

**External References:**
- [Anthropic Interviewer](https://www.anthropic.com/research/anthropic-interviewer) — AI-powered interviews research
- [Junior Architect Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-position-agreement-wilsch-ai-services) — Opera's design blueprint

**Session:**
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/ab077198-670d-47ef-b884-eca052c0b443.jsonl`

**Cross-references:**
- [#847 — Contract Strategy & Retainer Model](https://github.com/DaveX2001/deliverable-tracking/issues/847)
