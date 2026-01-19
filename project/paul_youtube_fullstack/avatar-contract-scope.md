---
publish: true
---

# Avatar Integration Contract Scope
[[client-paul]]

**Issue:** [#474](https://github.com/DaveX2001/deliverable-tracking/issues/474)

**Related:** [Avatar Research](./avatar-talking-head-research.md) | [Original Scope](./avatar-integration-scope.md)

---

## Context

YouTube demonetized Paul's fictional content channels (~€30k loss). Avatar overlay may restore monetization. Paul has agreed avatar is **paid work** separate from the 40% revenue share deal.

**Timeline:** 2 weeks target, blocking launch.

---

## Licensing Constraint

**HunyuanVideo-Avatar is excluded.** The [Tencent Community License](https://github.com/Tencent-Hunyuan/HunyuanVideo-Avatar/blob/main/LICENSE) explicitly states: *"THIS LICENSE AGREEMENT DOES NOT APPLY IN THE EUROPEAN UNION, UNITED KINGDOM AND SOUTH KOREA."* Paul operates from Germany (EU) → not legally usable.

**This is unfortunate because HunyuanVideo-Avatar would be preferred:**
- Accepts portraits at arbitrary scales/resolutions (no preprocessing needed)
- Supports emotion control via reference image
- Supports multi-character dialogue
- Lower VRAM (10GB via Wan2GP)

**EchoMimicV2 (Apache 2.0) is the only viable option:**
- Requires portrait pose-alignment preprocessing (additional scope)
- Half-body only (no head-only option)
- No emotion control
- 16GB VRAM, but 9x acceleration available (~50s for 120 frames on A100)

---

## Phase Structure

```
Phase 1 (Speed) → Phase 2 (Quality + Design) → Phase 3 (Integration)
    Discovery           Discovery + Design         Implementation
```

Each phase has a confidence gate. Work stops if a phase fails.

---

## Phase 1: Speed + Feasibility Spike

**Goal:** Benchmark both avatar models for quality comparison, then validate production viability.

| Atom | What | Output |
|------|------|--------|
| 1.1 | Deploy EchoMimicV2 on Modal | Working endpoint |
| 1.2 | Deploy HunyuanVideo-Avatar on Modal | Working endpoint (comparison only) |
| 1.3 | Build portrait preprocessing pipeline | Pose-alignment automation (EchoMimicV2) |
| 1.4 | Create input interface (TTS audio + portrait) | API accepting audio + image |
| 1.5 | Run benchmark using **real project audio** (10-min + 1-hour samples) | Timing + quality comparison |
| 1.6 | Test BG removal (Matanyone) | Transparent avatar video quality |
| 1.7 | Evaluate vs threshold (Paul defines) | Go/No-Go |

**Test inputs:** Use actual TTS audio from existing projects to validate audio compatibility with lip-sync.

**Models:**

| Model | Quality | VRAM | Purpose |
|-------|---------|------|---------|
| EchoMimicV2 | 80-85% | 16GB | Production candidate (Apache 2.0) |
| HunyuanVideo-Avatar | 85-90% | 10GB | Quality benchmark only (EU license excluded) |

**Note:** HunyuanVideo-Avatar is spiked for quality comparison data only. Due to EU licensing restriction, it cannot be used in production—EchoMimicV2 is the production candidate regardless of benchmark results.

**Gate criteria:**
- Processing time < Paul's acceptable threshold (for both 10-min and 1-hour videos)
- BG removal produces clean transparent output
- Portrait preprocessing works reliably

---

## Phase 2: Quality + Design

**Goal:** Validate output quality AND design integration architecture.

| Atom | What | Output |
|------|------|--------|
| 2.1 | Portrait spec discovery | Optimal image format/size |
| 2.2 | Quality review | Send snippets → Paul approves |
| 2.3 | Integration architecture design | How avatar fits into pipeline |

**Quality Approval Criteria (milestone-based):**
- Lip-sync is acceptable
- No visual artifacts
- Paul signs off

**Phase 2 deliverable:** "Quality approved" + "Here's how we'll integrate it"

---

## Phase 3: Integration + Deployment

> **Draft:** This phase is a preliminary outline. Actual scope will be validated after Phase 2's integration architecture design (atom 2.3).

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
| 1 | Single avatar for all channels, or different persona per channel? | Scope (one portrait vs multiple) |
| 2 | *(If per-channel)* How should portraits be sourced? You provide / Library / AI-generated? | Portrait workflow |
| 3 | Avatar position: Bottom-right? Side panel? Other? | Phase 3 (FFmpeg overlay config) |
| 4 | Avatar size: What % of frame? | Phase 3 (overlay dimensions) |
| 5 | Acceptable turnaround time for a 10-minute video? | Phase 1 (speed gate) |
| 6 | Acceptable turnaround time for a 1-hour video? | Phase 1 (speed gate) |

**Note:** Style question (head vs half-body) removed. EchoMimicV2 is half-body only.

---

## Contract Structure (Internal)

**Phased with milestone gates:**

| Phase | Nature | Gate |
|-------|--------|------|
| 1: Speed + Feasibility | Discovery | Turnaround acceptable? BG removal clean? |
| 2: Quality | Discovery + Design | Lip-sync OK, no artifacts, Paul signs off |
| 3: Integration | Implementation | Avatar working in pipeline |

**Key insight:** Phases 1-2 are "paid discovery" with uncertain outcomes. Phase 3 is predictable engineering once discovery passes.

---

## Next Steps

1. Send questions to Paul (7 questions above)
2. Define pricing per phase
3. Create formal contract/proposal
4. Begin Phase 1 after contract signed
