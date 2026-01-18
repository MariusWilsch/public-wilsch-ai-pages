---
publish: true
---

# Avatar Integration Contract Scope
[[client-paul]]

**Date:** 2026-01-18

**Status:** Scope Complete, Questions Pending

**Issue:** [#474](https://github.com/DaveX2001/deliverable-tracking/issues/474)

**Related:** [Avatar Research](./avatar-talking-head-research.md) | [Original Scope](./avatar-integration-scope.md)

---

## Context

YouTube demonetized Paul's fictional content channels (~€30k loss). Avatar overlay may restore monetization. Paul has agreed avatar is **paid work** separate from the 40% revenue share deal.

**Timeline:** 2 weeks target, blocking launch.

---

## Phase Structure

```
Phase 1 (Speed) → Phase 2 (Quality + Design) → Phase 3 (Integration)
    Discovery           Discovery + Design         Implementation
```

Each phase has a confidence gate. Work stops if a phase fails.

---

## Phase 1: Speed Spike

**Goal:** Prove avatar generation is fast enough for production.

| Atom | What | Output |
|------|------|--------|
| 1.1 | Deploy HunyuanVideo-Avatar + EchoMimicV2 on Modal | Two working endpoints |
| 1.2 | Create input interface (TTS audio + portrait) | API accepting 24kHz WAV + image |
| 1.3 | Run benchmark (short clip + full 10-min) | Timing data for both models |
| 1.4 | Evaluate vs threshold (Paul defines) | Model recommendation + Go/No-Go |

**TTS Output Format (from codebase):**
- Sample rate: 24,000 Hz
- Channels: Mono
- Bit depth: 32-bit Float
- Path: `{transcript_dir}/audio/audio_narrative_transcript.wav`

**Models to test:**

| Model | Quality | VRAM | Notes |
|-------|---------|------|-------|
| HunyuanVideo-Avatar (via Wan2GP) | 85-90% | 10GB | Best quality, low VRAM mode |
| EchoMimicV2 | 80-85% | 16GB | Apache 2.0, 9x speedup optimization |

**Gate criteria:** Processing time < Paul's acceptable threshold for 10-min video.

---

## Phase 2: Quality + Design

**Goal:** Validate output quality AND design integration architecture.

| Atom | What | Output |
|------|------|--------|
| 2.1 | BG removal test | Transparent avatar video |
| 2.2 | Portrait spec discovery | Optimal image format/size |
| 2.3 | Quality review | Send snippets → Paul approves |
| 2.4 | Integration architecture design | How avatar fits into pipeline |

**BG Removal (conditional on Phase 1 winner):**
- If HunyuanVideo-Avatar wins → Use Wan2GP's built-in Matanyone
- If EchoMimicV2 wins → Integrate standalone Matanyone

**Phase 2 deliverable:** "It works" + "Here's how we'll integrate it"

---

## Phase 3: Integration + Deployment

**Goal:** Wire avatar into production pipeline.

| Atom | What | Output |
|------|------|--------|
| 3.1 | Create Modal avatar service | `modal/avatar/app.py` |
| 3.2 | Pipeline integration | Avatar step parallel to video segments |
| 3.3 | FFmpeg overlay | Composite avatar on slideshow |
| 3.4 | Parallelization (if needed) | Split audio for parallel workers |
| 3.5 | RunPod deployment | Production endpoint |

---

## Architecture: Parallel Processing

Avatar runs **parallel** to Video Segments (both after TTS):

```
Scene Extraction (1) ──┬──→ Image Gen (2) → Video Segments (4) ──┐
                       │                                          │
                       └──→ TTS (3) → Avatar Gen (NEW) ──────────┼──→ FFmpeg Overlay → Final
                                                                  │
                                                                  └───────────────────────┘
```

**Why parallel?**
- Avatar needs TTS audio (for lip sync)
- Avatar does NOT need slideshow images
- Both can run simultaneously after TTS completes
- FFmpeg composites at the end

**Current pipeline (from codebase):**

| Phase | What | Where |
|-------|------|-------|
| 1 | Scene Extraction | Local |
| 2 | Image Gen (FLUX) | Modal (H100 GPU) |
| 3 | TTS (Chatterbox) | Modal (L4 GPU) |
| 4 | Video Segments (Ken Burns) | Modal (CPU workers) |
| 4' | **Avatar Gen (NEW)** | Modal (GPU) |
| 5 | FFmpeg Concat + Overlay | Local |
| 6 | Upload to Supabase | Local |

---

## Questions for Paul (Before Contract)

| # | Question | Affects |
|---|----------|---------|
| 1 | Avatar persona: Single or per-channel? | Scope (one portrait vs multiple) |
| 2 | Reference image source: Stock/AI/Custom? | Who provides portrait |
| 3 | Avatar position: Bottom-right? Side panel? | Phase 3 (FFmpeg overlay config) |
| 4 | Avatar size: What % of frame? | Phase 3 (overlay dimensions) |
| 5 | Avatar style: Head only or half-body? | Phase 1 (model selection) |
| 6 | Acceptable processing time for 10-min video? | Phase 1 (speed gate criteria) |

---

## Contract Structure (Internal)

**Phased with milestone gates:**

| Phase | Nature | Gate |
|-------|--------|------|
| 1: Speed | Discovery | Generation time acceptable? |
| 2: Quality | Discovery + Design | Paul approves snippets? |
| 3: Integration | Implementation | Avatar working in pipeline |

**Key insight:** Phases 1-2 are "paid discovery" with uncertain outcomes. Phase 3 is predictable engineering once discovery passes.

---

## Next Steps

1. Send questions to Paul (6 questions above)
2. Define pricing per phase
3. Create formal contract/proposal
4. Begin Phase 1 after contract signed
