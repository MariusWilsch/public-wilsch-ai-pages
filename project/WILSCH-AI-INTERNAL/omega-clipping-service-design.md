---
publish: true
---

# Omega AI Video Clipping Service
[[omega-clipping-service-design]]

Design doc for an AI-powered service that extracts "pearls" (high-value teaching moments) from Omega Bone's vocal coaching content and produces post-ready short-form clips for social media.

---

## Problem Statement

Omega Bone is a vocal coach with 25+ years of experience and 7,400+ students trained worldwide. She has accumulated a large library of educational singing content — a 10-track album ("Come With Me") and ~300 YouTube class recordings ("Learn to Sing with Omega", COVID era, 2-3h each) — that contains high-value moments buried in hours of raw footage.

These moments — coaching breakthroughs, authenticity discoveries, quotable teaching wisdom — are what Omega calls "pearls." She cannot efficiently identify and extract them manually: "sorting through them makes my brain and my eyes bleed." Generic clipping tools like Opus Clip fail because they detect topics and engagement signals, not the domain-specific coaching moments that constitute Omega's value. As her business advisor Anthony put it: "there's no topic" for generic tools to latch onto.

Omega needs an AI-powered service that understands her teaching domain well enough to extract pearls from raw content and produce post-ready short-form clips for social media (YouTube Shorts, TikTok, Instagram Reels), feeding her marketing funnel: clips → website → discovery session → paid programme.

This design doc does NOT cover: website development, pricing strategy, album production, or the programme structure itself.

**Preconditions:**
- "Come With Me" album exists (10 tracks, produced music videos on YouTube)
- ~300 Learn to Sing class recordings exist on YouTube (Streamyard → YouTube)
- Business funnel is defined (Figma website redesign, product matrix: $1k–$10.5k LTV)
- Persona document exists ([Burden Index, 52 problems](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-persona-burden-index))
- 2026 1-on-1 lessons confirmed as primary content source (recorded at NS)
- Pearl detection POC validated (6 pearls from 3 videos, narrative arc signal)
- Album release date: March 29th (urgency driver)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Omega has a managed AI service that extracts pearls from her content library and delivers post-ready short-form clips she can publish directly to social media. |
| **Success** | Omega reviews a batch of AI-generated clips from her album and class recordings, finds them publishable without manual editing, and confirms they match her teaching philosophy — authenticity over technical perfection, "the next natural singer, not another Whitney Houston." |
| **Done test** | Can we write a meeting agenda with open design questions about how the clipping service works? If no → the design is complete and ready for implementation. |

---

## Approach

### Part 1: Data Sources & Input Types

Three content types serve the service, each with a distinct role:

**Primary (Pearl Source) — 2026 1-on-1 Lessons**
Current individual sessions recorded at Network School (NS). One student per session, ~42 minutes. Higher video and audio quality than legacy recordings. Structure: Omega's intro song → standard welcome → student introduction (background + why they're here) → song selection → warm-up → performance → coaching feedback. The student introduction segment naturally surfaces problem identification pearls (stage fright, vocal struggles, dream outcomes). The perform→coach→breakthrough arc is the same as group classes but with deeper, longer interactions per student.

**Secondary (Pearl Source, Deprioritized) — Learn to Sing Group Classes (~300 videos)**
COVID-era live-streamed classes via Streamyard, published to YouTube (mirrored on Facebook). Zoom-grid format with ~10-25 students on camera, 2-3 hours each. Deprioritized due to low video quality — participants joined from Southeast Asia, Central and South Africa with limited internet bandwidth (2020 era). ~5 clippable moments per class, but clip quality constrained by source quality.

**Branding Element — "Come With Me" Album (10 tracks)**
Produced music videos featuring nature footage with lyric overlays and Omega's original songs. Album release: March 29th, $150/download, vinyl + merch kit at 1,000 sales. The album is NOT a pearl extraction source — it provides outro clips for short-form videos (every clip ends with an album song snippet for brand recognition) and sits in Omega's product stack as a standalone monetization stream.

**Reference:** [Persona Burden Index (52 problems)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-persona-burden-index) — target avatar document describing psychographic profile and pain points.

### Part 2: Target Avatar & Content Strategy

The target avatar is psychographic, not demographic: creative adults who carried a childhood dream of singing but never pursued it. Anthony (business advisor) defined the exclusions: not entrepreneurs, not parents — "there's no mention whatsoever of children, business, Entrepreneur on the YouTube channel." Omega clarified: "It's not feminine. It's the creator."

Clips serve Omega's marketing funnel:
- **Top of funnel:** Clips drive awareness across three personas — Learn to Sing, Learn to Communicate with Song, and Music Room 33. Clip emotion: accessibility — "I could do that too."
- **Mid funnel:** 1-on-1 lesson clips show transformation proof. Clip emotion: confidence — "look how far they came."
- **Conversion:** All clips CTA to "book a free vocal analysis call" (Omega's "diagnostics") via scheduling link or QR code — NOT to omegabone.com (website is being rebuilt). Sales call → product matrix.

**Product Matrix (all three personas funnel into one package):**

| Tier | Price | Content |
|------|-------|---------|
| Downsell | $1,000/mo | 1 song, vocal training |
| Main | $5,000 | 12-week concert programme — 6 songs, culminates in live concert |
| + Recording | +$2,500 | Concert recording / album |
| + Songwriting | +$3,000 | Original songwriting training |
| **Max LTV** | **$10,500** | |

### Part 3: Pearl Taxonomy

A "pearl" is a high-value moment worth extracting as a short-form clip. Omega's pearls follow a narrative arc: relatable starting point → coaching moment → breakthrough. This matches the testimonial format on her website redesign.

Five pearl categories confirmed across two meetings (discovery call + in-person meeting):

1. **Coaching feedback moments** — Omega giving specific, actionable advice (e.g., "lean into your French accent, don't try to obscure it")
2. **Authenticity breakthroughs** — A student singing naturally for the first time, finding their own voice ("I'm looking for the next natural singer")
3. **Transformation arcs** — The before→coaching→after journey showing a student's progression (e.g., Ira's recovery story woven into "Space Oddity" performance)
4. **Quotable wisdom** — One-liners suitable for text overlay or Pinterest images ("A baby can hoop and holler all day and not get hoarse. Because they're screaming on their breath.")
5. **Problem identification** — Students articulating their vocal struggles, failed attempts, and dream outcomes. Follows a sales framework: problem → what they've tried → struggles → aspirational goal. Naturally surfaces during the student introduction segment of 1-on-1 lessons (e.g., "I wanted to get over my stage fright and be able to sing in front of other people").

**Detection signal:** The narrative arc (relatable starting point → coaching moment → breakthrough) reliably identifies pearls across all 5 categories. Validated by POC: 6 pearls extracted from 3 class recordings, ~2 strong candidates per 30-40 min session. Problem identification (category 5) validated against 1-on-1 lesson format — the student introduction segment contains natural problem/dream outcome framing.

### Part 4: Processing Pipeline

Abstract pipeline with Claude Code SDK as the chosen implementation approach:

1. **Ingest** — Download video from YouTube, extract audio track, generate transcript
2. **Analyze** — AI reviews transcript + audio for pearl signals, identifies candidate clip timestamps. Domain knowledge (Omega's teaching philosophy, pearl taxonomy) is primed into the model context.
3. **Clip** — Extract video segments at identified timestamps, add captions from transcript, format per platform specifications. No background music — teaching content stays clean. Album song snippet appended as outro (brand recognition).
4. **Stage** — Output clips to staging area (unlisted YouTube or Google Drive) for Omega's review and approval before publishing. Omega curates sensitive content (e.g., student stories involving personal topics) during this step.

**Validated toolchain (POC):** `yt-dlp` (video/audio download) → AssemblyAI CLI (transcription) → Claude (pearl detection against taxonomy). POC: 6 pearls from 3 videos, ~2 candidates per 30-40 min session.

The approach is iterative: ship a first batch fast, Omega reviews ("you're gonna hate it, you're gonna love it"), feedback improves the model's understanding of what constitutes a pearl. Not a traditional software project — a primed AI agent that improves through use.

### Part 5: Output Specification

Deliverable = post-ready short-form clips that Omega can publish directly.

**Primary platforms (Phase 1):**
- YouTube Shorts (~60 seconds, vertical)
- TikTok (shorter than 60s, vertical)
- Instagram Reels (vertical, up to 90s)

**Clip format (confirmed):**
1. **No intro** — start immediately with hook
2. **Hook** (first 2 seconds) — pearl moment OR student stating their problem
3. **Content** — pearl / teaching moment
4. **Outro** — album song clip (brand recognition, fills to ~60s if pearl is shorter)
5. **Branding** — purple / lavender frame throughout all videos
6. **Duration** — ~60 seconds target
7. **CTA** — "Book a free vocal analysis call" via description link + QR code overlay linking to Omega's scheduling calendar

**Consent:** All source videos are public YouTube recordings — no separate consent required. Sensitive content (e.g., personal stories involving recovery, health) is curated by Omega during the staging/approval step.

**Deferred platforms:** Pinterest (quote graphics from legacy class recordings) and LinkedIn (highest revenue platform) are not in current scope. Omega's priority is revenue-generating platforms first (YT/TT/IG). Pinterest and LinkedIn may be added in a future phase.

### Part 6: Delivery Model

Managed service — VeloxForce operates the clipping service, Omega is the client.

**Phase 1: Batch Processing**
Process 2026 1-on-1 lessons first (priority), then legacy class recordings as quality permits. AI extracts all potential clips. Omega curates — reviews staged clips and approves for publishing. No volume cap on extraction.

**Phase 2: Ongoing Processing**
Recurring content: Sunday group classes + 10 hours/week private lessons. Expected cadence: ~5 clips per class, ~1 clip per private lesson. Omega indicated a year-long engagement timeline.

**Engagement model:** Monthly retainer (not project-based). Initial period focuses on building the AI's understanding of Omega's pearl taxonomy and establishing the clip production rhythm. Performance-based component (rev share) considered after the initial ramp-up period. Omega expressed preference for long-term retainer over fixed project: "I'm here long term." Specific pricing in the [commercial doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-commercial-design-doc).

---

## Source

- **Transcripts:**
  - [Discovery Call 2026-03-08 (52 min)](https://drive.google.com/file/d/1xnIw-xMsN-KZXO72BA4z6DyYAq37wtBd/view) — Omega + Anthony + Marius
  - [Grooming 2026-03-08](https://drive.google.com/file/d/1fwumO3pBf5tRiCy08BU_qoPtxnzaj-m4/view)
  - [Fireflies Transcript](https://app.fireflies.ai/view/01KK6JXB75WTRS613Y4S56T5Z8)
  - [In-Person Meeting 2026-03-18 (~1h)](https://drive.google.com/file/d/1h-YrcQda38girPmoor3Ndsb8hqQM2jrk/view) — Omega + Marius at NS
- **Artifacts:**
  - [Figma: Vocal Coach Website Redesign](https://github.com/user-attachments/files/25824674/screencapture-figma-make-I5V9i7drJ3FCziHPcPHmDh-Vocal-Coach-Website-Redesign-2026-03-08-22_50_40.pdf) — 3-tier programme, avatar messaging, testimonial format
  - [YouTube: Come With Me Album (playlist)](https://www.youtube.com/playlist?list=PLFWa3WnH-5diGa82MJNQB0sgGKMzO89pz) — 10 tracks, album content
  - [YouTube: Learn 2 Sing with Audrey (live)](https://youtube.com/live/Odr_khKGmuc) — Recent class format example
  - [YouTube: Ep. 2 Learn to Sing (2021)](https://youtu.be/XxCaFDsWPfs) — Legacy class format example
  - [Epic #1071](https://github.com/DaveX2001/deliverable-tracking/issues/1071) | [Design Issue #1076](https://github.com/DaveX2001/deliverable-tracking/issues/1076)
  - [Pearl POC Results](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-pearl-poc-results) — 6 pearls from 3 videos
  - [Commercial Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-commercial-design-doc)
  - [Contract Strategy & Retainer Model](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/contract-strategy-retainer-model-design)
- **Session:**
  - Pass 1: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/bc288ebc-dddf-4d6b-aeb6-39b09aac97f0.jsonl
  - Pass 5: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-09-SALES-PIPELINE--deliverable/ffc1ee74-f0a9-4f7a-804c-cc74126b0ea1.jsonl
