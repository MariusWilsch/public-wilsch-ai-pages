# VideoGen Scaling Spike - RunPod Containerization
[[client-paul]]

---
publish: true
date: 2025-12-19
issue: 329
---

## Decision

**Containerize Express backend on RunPod Serverless**

Confidence: 88%

## Context

- Goal: Decouple from GPU box, scale horizontally like TTS
- Current: Flask → Express (nuca-systems GPU) → video
- After: Flask → RunPod (Express container) → video

## Key Findings

### 1. Current Pipeline Timing

- Videos are 1-2 hours long (long-form YouTube)
- NVENC encodes at 0.6x realtime (working correctly)
- 200 users × 1.5h videos/day = ~12 GPUs needed

### 2. Architecture Options Evaluated

| Option | Verdict | Reason |
|--------|---------|--------|
| Cloud Encoding (Mux/CF) | Rejected | 15GB intermediate uploads per video |
| Python Handler Rewrite | Rejected | 1820 lines to rewrite, high risk |
| **Containerize Express** | Winner | 130 lines changed, same TTS pattern |

### 3. Why Express Container Wins

- **Transfer:** 50MB assets vs 15GB intermediate video
- **Code risk:** ~130 lines changed (GPU detection, CPU fallbacks)
- **Pattern:** Identical to TTS (proven, familiar)
- **Timeline:** 2-3 weeks vs 6-8 weeks for rewrite

## Recommended Architecture

```
Before:  Flask (CPU) → Express (nuca-systems GPU) → video
After:   Flask (CPU) → RunPod (Express container) → video
```

### Container Spec

- Base: `nvidia/cuda:12.x` + Node.js 20 + FFmpeg/NVENC
- Handler: Thin wrapper around existing generateSlideshow.ts
- Input: Images + audio + config (~50MB)
- Output: Direct upload to Supabase Storage

## Implementation Phases

### Phase 1 (Week 1-2): Container + Handler

- Dockerfile with nvidia/cuda base
- Remove ~130 lines GPU detection code
- Add RunPod handler interface

### Phase 2 (Week 2-3): Flask Integration

- Mirror TTS dispatch pattern
- Status polling, completion handling
- Direct Supabase upload from handler

### Phase 3 (Week 3-4): Validation + Cutover

- Parallel run for quality parity
- Production migration

## Cost Model

| Scale | Monthly Cost |
|-------|--------------|
| Low volume | $50-200 (scale to zero) |
| 200 users | $4,000-6,600 |
| Decision point | If >$3K/mo → evaluate dedicated GPU |

## Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| Long job failures (2-3h) | Segment checkpointing, retry logic |
| Cost at scale | Monitor, switch to dedicated at threshold |
| Container complexity | Use official nvidia base, proven FFmpeg |

## Code Analysis

### Safe to Delete (~130 lines)

- GPU detection functions (lines 197-245)
- Testing mode conditionals (lines 606-638)
- CPU fallback block (lines 1740-1757)
- Multi-codec selection (can hardcode h264_nvenc)

### Risky to Delete (test first)

- Subtitle CPU fallback (lines 1154-1157) - NVENC + libass workaround
- Video loop CPU fallback (lines 926-928) - encoding hang workaround

### Must Keep

- Ken Burns logic (core creative feature)
- FFmpeg filter_complex (composition engine)
- RTX 5080 optimizations (benefit all GPUs)

## Related Documents

- [TTS Pipeline Architecture](tts-pipeline-architecture.md)
- [TTS Vertical Slices](tts-vertical-slices.md)
- [Pipeline Orchestration Design](pipeline-orchestration-design.md)

## Next Step

Create implementation issue for Express → RunPod containerization
