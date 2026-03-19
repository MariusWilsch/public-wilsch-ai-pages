---
publish: true
---

# Omega AI Video Clipping Service — Meeting Agenda
[[omega-clipping-meeting-agenda]]

Meeting agenda for resolving open design questions from the [design doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-clipping-service-design).

## Meeting Goal

✅ **Meeting held 2026-03-18 at NS.** All four design questions resolved — resolutions integrated into design doc Pass 5.

1. ✅ **Pearl signal definition** — 5 categories confirmed (coaching feedback, authenticity, transformation arcs, quotable wisdom, problem identification). POC validated narrative arc detection.
2. ✅ **Platform priority** — YT/TT/IG confirmed. Pinterest/LinkedIn deferred.
3. ✅ **Content boundaries** — Public YouTube = fair game. Omega curates sensitive content in staging. 1-on-1 format documented.

## Pre-Read

- [Omega AI Video Clipping Service — Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-clipping-service-design) — specifically the **Undefined** markers in Part 3 (Pearl Taxonomy) and Part 5 (Output Specification)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Student consent for featured clips
⏱️ 5 min

✅ **Resolved:** Public YouTube live streams = no consent issue. Sensitive content (personal stories) curated by Omega during clip approval. No formal consent process with individual students needed.

The Learn to Sing classes were public live streams on YouTube — students joined knowing they were recorded. Using individual students as featured subjects in marketing clips (especially transformation arcs tracking one person across sessions) is a different use case than the original class recording.

- Classes had 10-25 students on camera in Zoom grid format
- Transformation arcs require identifying and featuring specific individuals
- Three students (Simon, Anthony, Ira) are in an active concert program producing story arcs by April

**To resolve:** The consent framework for featuring individual students in marketing clips derived from class recordings.

### 2. Class recording structure
⏱️ 5 min

✅ **Resolved:** 1-on-1 format confirmed as primary source. Structure: intro song → welcome → student intro → song → warmup → perform → coach. ~42 min per session. Group class structure deprioritized (quality).

The AI needs to segment 2-3 hour class recordings into clippable units. The working assumption is: student performs → Omega gives feedback → next student, with ~5 performances per class. Additional structural elements (warm-up, group exercises, introductions) would change how the AI identifies segment boundaries.

- Current classes (e.g., "Learn 2 Sing with Audrey") run ~42 minutes
- Legacy classes (COVID era) ran 2-2.5 hours with ~25 students
- Students choose their own songs

**To resolve:** The typical flow of a class recording from start to finish, including any recurring segments beyond individual performances.

### 3. Platform priority beyond video clips
⏱️ 5 min

✅ **Resolved:** YouTube Shorts + TikTok + Instagram Reels confirmed. Pinterest/LinkedIn deferred — "focus on what's going to make more money."

Phase 1 covers video clips for YouTube Shorts, TikTok, and Instagram Reels. Two additional platforms were discussed but not prioritized: Pinterest requires image + text posts (not video), and LinkedIn is where Omega reported making the most money — yet it wasn't discussed as a clip destination.

- Pinterest: "just pictures. Pictures with the words" (Omega)
- LinkedIn: highest revenue platform, corporate gig source
- Each additional platform adds a distinct output pipeline

**To resolve:** Which platforms beyond YT/TT/IG should be included in the service scope, and in what priority order.

### 4. Pearl signal definition for AI detection
⏱️ 10 min

✅ **Resolved:** 5 pearl categories confirmed. Narrative arc (before→coaching→after) validated by POC as primary detection signal. Problem identification (5th category) grounded in 1-on-1 student intro segment.

Four pearl categories were identified from the discovery call: coaching feedback moments, authenticity breakthroughs, quotable wisdom, and tidbits. These describe WHAT a pearl is, but the AI needs specific signals to detect them — audio patterns, language cues, or structural markers in the recordings.

- "The meat and potatoes. No salad, no garnish." (Omega on what she wants)
- "Lean into your French accent" — example of coaching feedback pearl
- "I'm not looking for another Whitney Houston" — Omega's teaching philosophy
- The website testimonial format shows the desired narrative arc

**To resolve:** The specific observable signals Omega associates with each pearl category, grounded in examples from her own content.

## Meeting Format

- **Type:** Discovery / alignment
- **Duration:** ~25 min
- **Expectation:** Omega reviews the design doc Pre-Read beforehand, brings examples of "pearls" she's already identified in her content
- **Outcome:** All four Undefined markers in the design doc can be resolved → design complete

## Related

- **Issue:** [Epic #1071](https://github.com/DaveX2001/deliverable-tracking/issues/1071) | [Design #1076](https://github.com/DaveX2001/deliverable-tracking/issues/1076)
- **Design Doc:** [omega-clipping-service-design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-clipping-service-design)
