---
publish: true
---

# Ken Burns Optimization: Spike Results
[[client-paul]]

## Executive Summary

**Hypothesis:** OpenCV CUDA would provide 2x+ speedup over FFmpeg zoompan.
**Result:** OpenCV CUDA is 0.15x (7x slower) due to GPU→CPU transfer overhead.
**Unexpected finding:** OpenCV CPU is 3500 fps - already extremely fast.
**Root cause discovered:** FFmpeg zoompan rebuilds SwsContext per-frame (the real bottleneck).

## The Real Bottleneck

FFmpeg's `zoompan` filter **rebuilds the entire scaling pipeline from scratch for EVERY FRAME**:

```c
// Per frame in vf_zoompan.c:
s->sws = sws_alloc_context();        // allocate
sws_init_context(s->sws, ...);       // compute filter coefficients (EXPENSIVE)
sws_scale(s->sws, ...);              // actual scaling (fast)
sws_freeContext(s->sws);             // free everything
```

The 60% CPU in `hscale` functions isn't scaling pixels - it's recomputing bicubic filter coefficients 30 times per second.

## Spike Benchmark Results

| Approach | Speed | Why |
|----------|-------|-----|
| FFmpeg zoompan | ~33 min for 6hr video | Per-frame SwsContext rebuild |
| OpenCV CUDA | 290 fps (0.15x) | GPU→CPU transfer overhead |
| **OpenCV CPU** | **3500 fps** | No context overhead |

### Parallel Benchmark (Dec 22, 2024)

Tested on nuca-systems (32 cores, RTX 5080):

| Workers | Time (100 clips) | Per Clip | Speedup |
|---------|------------------|----------|---------|
| 1 | 24.02s | 0.240s | 1.0x |
| 4 | 7.16s | 0.072s | 3.4x |
| 8 | 5.35s | 0.053s | 4.5x |
| **16** | **4.51s** | **0.045s** | **5.3x** |
| 32 | 4.55s | 0.046s | 5.3x |

Diminishing returns after 16 cores (likely disk I/O bottleneck).

### 6-Hour Video Projection

| Approach | Ken Burns Time | Full Pipeline |
|----------|----------------|---------------|
| FFmpeg zoompan | ~28 min | ~33 min |
| OpenCV (1 worker) | ~29 min | ~34 min |
| **OpenCV (16 workers)** | **~5.4 min** | **~10-11 min** |

**3x overall pipeline speedup** for long-form content.

## Why OpenCV CUDA Failed

Each of 90 frames per image requires `result.download()` (GPU→CPU transfer):

```python
for frame in range(90):
    result = cv2.cuda.warpAffine(gpu_img, M, size)
    frames.append(result.download())  # 90 transfers per image!
```

Transfer overhead dominated any GPU compute gains.

## Why OpenCV CPU Wins

OpenCV's `cv2.resize()` doesn't rebuild context per-frame:
- No `sws_init_context()` equivalent
- Reuses optimized SIMD paths
- Direct memory access

## Solution Architecture

Replace zoompan with OpenCV CPU, pipe to FFmpeg for encoding only:

```
Current:  Image → [FFmpeg zoompan (slow)] → Encoded video

Proposed: Image → [OpenCV CPU Ken Burns (fast)] → [FFmpeg encode only] → Video
```

| Step | Tool | Why |
|------|------|-----|
| Ken Burns frames | OpenCV CPU | 3500 fps, no context rebuild |
| Video encoding | FFmpeg NVENC | GPU encoding, already fast |
| Transitions | FFmpeg xfade | Only 2% CPU, not bottleneck |
| Audio | FFmpeg | Already fast |

## Implementation Notes

OpenCV CPU prototype (tested on nuca-systems):
```python
def ken_burns_cpu(img, frames=90, zoom=0.08):
    h, w = img.shape[:2]
    for i in range(frames):
        t = i / frames
        crop_w = int(OUTPUT_WIDTH / (1.0 + t * zoom))
        crop_h = int(OUTPUT_HEIGHT / (1.0 + t * zoom))
        x = (w - crop_w) // 2 + int(t * (w - crop_w) * 0.3)
        y = (h - crop_h) // 2
        frame = cv2.resize(img[y:y+crop_h, x:x+crop_w], (OUTPUT_WIDTH, OUTPUT_HEIGHT))
        yield frame
```

## Infrastructure Note

Getting OpenCV CUDA working required:
- cudawarped pre-built wheel (official PyPI has no CUDA)
- cuDNN 9.x installation
- FFmpeg 4.x compatibility libs (symlinks to newer versions)
- Total setup: ~1 hour

This complexity is not needed since CPU solution is faster.

## Key Learnings Summary

### 1. The Bottleneck Discovery

**FFmpeg zoompan is slow because:**
- It rebuilds `SwsContext` (scaling pipeline) for EVERY frame
- 86% of CPU time spent in `libswscale` functions
- GPU sits idle at 2% while CPU maxes out at 158%

**OpenCV is fast because:**
- `cv2.resize()` reuses internal structures
- No per-frame context rebuild
- Direct memory access, SIMD optimized

### 2. The Benchmark Results

| Approach | 6hr Video Time |
|----------|----------------|
| FFmpeg single-pass | ~33 min |
| OpenCV sequential | ~27 min |
| **OpenCV 16 workers** | **~5.4 min** |

Sequential OpenCV is marginally faster. **The real win is parallelization** - images are independent, so 16 workers = 6x speedup.

### 3. How OpenCV Ken Burns Works

```python
def ken_burns(image, duration, zoom_percent=8, direction='in'):
    frames = int(fps * duration)

    for frame_num in range(frames):
        t = frame_num / frames  # 0 → 1 progress

        # Calculate zoom (replicates FFmpeg zoompan math)
        zoom = 1.0 + (zoom_percent/100 * t)

        # Crop window shrinks as zoom increases
        crop_w = output_width / zoom
        crop_h = output_height / zoom

        # Extract and resize
        cropped = image[y:y+crop_h, x:x+crop_w]
        frame = cv2.resize(cropped, (output_width, output_height))

        yield frame
```

### 4. Integration Plan

```
Current:   images → FFmpeg zoompan → xfade → encode
                    (slow, sequential)

Proposed:  images → OpenCV (parallel) → clips → FFmpeg xfade → encode
                    (fast, 16 workers)         (unchanged)
```

**Only Ken Burns moves to OpenCV. xfade, overlay, encode stay in FFmpeg.**

## Sources

- Spike testing: nuca-systems, Dec 2024
- FFmpeg source: `libavfilter/vf_zoompan.c`
- Issue: [#329 VideoGen Scaling Spike](https://github.com/DaveX2001/deliverable-tracking/issues/329)
- Conversation: Dec 22, 2024
