# VideoGen Scaling Spike - Investigation Journey
[[client-paul]]

---
publish: true
date: 2025-12-19
updated: 2025-12-23
issue: 329
---

## Final Decision

**OpenCV CPU for speed, RunPod CPU workers for scale**

The original GPU-focused approach was invalidated. The bottleneck is CPU (filter processing), not GPU (encoding).

## Investigation Timeline

### Phase 1: Original Hypothesis (Dec 19)

**Assumption:** GPU encoding is the bottleneck → need GPU workers on RunPod

Recommended: Containerize Express on RunPod with GPU workers (88% confidence)

### Phase 2: Benchmarking Revealed Truth (Dec 21)

Instrumented the pipeline. Found:
- GPU utilization: **2%** (idle, starved for frames)
- CPU utilization: **158%** (bottleneck)
- FFmpeg's `zoompan` filter rebuilds SwsContext per-frame (86% of CPU time)

**Original hypothesis invalidated.** GPU isn't the problem.

### Phase 3: OpenCV Discovery (Dec 22)

Tested alternatives:
| Approach | Result | Why |
|----------|--------|-----|
| FFmpeg zoompan | ~33 min/6hr video | Per-frame context rebuild |
| OpenCV CUDA | 0.15x (7x slower) | GPU→CPU transfer overhead |
| **OpenCV CPU** | **3500 fps** | No context rebuild |
| OpenCV CPU (16 workers) | ~5.4 min/6hr video | **6x speedup** |

**Root cause:** FFmpeg zoompan rebuilds the entire scaling pipeline for EVERY FRAME. OpenCV's `cv2.resize()` reuses internal structures.

### Phase 4: Revised Architecture (Dec 23)

```
Speed solution:  OpenCV CPU (parallel) replaces FFmpeg zoompan
Scale solution:  RunPod CPU workers (not GPU - cheaper!)
```

Since the bottleneck is CPU, not GPU:
- Don't need expensive GPU workers
- RunPod offers CPU-only endpoints
- Same scaling pattern, lower cost

## Current Path Forward

1. **Validate OpenCV integration** (Slices 1-4 in [vertical slices doc](videogen-opencv-vertical-slices.md))
2. **If validated:** Revisit RunPod with CPU worker framing
3. **Implementation issues:** #333, #334 deprioritized pending OpenCV validation

## Key Learnings

1. **Measure before architecting.** Original spike assumed GPU was the bottleneck without profiling.
2. **CPU filters can bottleneck GPU pipelines.** The GPU sat idle at 2% while CPU maxed out.
3. **RunPod works for CPU workloads too.** Don't assume serverless = GPU-only.

## Archived: Original GPU Approach

<details>
<summary>Original recommendation (superseded)</summary>

### Original Architecture
```
Before:  Flask (CPU) → Express (nuca-systems GPU) → video
After:   Flask (CPU) → RunPod (Express container with GPU) → video
```

### Why It Was Wrong
- Assumed GPU encoding was the bottleneck
- Didn't profile the actual pipeline
- Missed that zoompan (CPU filter) was 86% of processing time

### Container Spec (No Longer Relevant)
- Base: `nvidia/cuda:12.x` + Node.js 20 + FFmpeg/NVENC
- Handler: Thin wrapper around existing generateSlideshow.ts
- Input: Images + audio + config (~50MB)
- Output: Direct upload to Supabase Storage

</details>

## Related Documents

- [OpenCV Ken Burns Solution](opencv-cuda-ken-burns-solution.md) - Technical findings
- [VideoGen Pipeline Architecture](videogen-pipeline-architecture.md) - Current pipeline
- [OpenCV Vertical Slices](videogen-opencv-vertical-slices.md) - Implementation plan
