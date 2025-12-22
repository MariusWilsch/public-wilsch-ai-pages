---
publish: true
---

# OpenCV CUDA for Ken Burns: Solving the zoompan Bottleneck
[[client-paul]]

## Problem Statement

FFmpeg's `zoompan` filter creates Ken Burns effects by scaling an 8000px source image down to 1344px output resolution **for every frame** (550 frames per image at 30fps). This per-frame scaling consumes ~60% CPU while the GPU sits idle at 1%.

**Root cause:** zoompan is CPU-only. Each frame requires:
- Calculate crop region (zoom window position)
- Scale 8000px → 1344px via libswscale
- Repeat 550 times per image

## Why FFmpeg GPU Scaling Failed

Attempted solution: `scale_cuda` filter for GPU-accelerated scaling.

**Result:** No improvement. Perf profiling revealed:

| Metric | CPU scale | GPU scale_cuda |
|--------|-----------|----------------|
| hscale functions | 60% | 60% |
| Memory transfer | 1.4% | **10.4%** |

**Root cause:** FFmpeg's filter pipeline is CPU-centric. Data flows:
```
CPU → upload → GPU scales → download → CPU → next filter
```

The 10.4% `__memmove` overhead (CPU↔GPU transfers) negates any GPU compute gains.

## Solution: OpenCV CUDA

OpenCV CUDA solves the transfer problem by **keeping data in GPU memory** across operations.

**Key difference:**
```
FFmpeg: CPU → GPU → CPU → GPU → CPU (per frame)
OpenCV: CPU → GPU (stays there for 550 frames) → CPU
```

### Implementation Approach

```python
import cv2

# Upload image ONCE
gpu_image = cv2.cuda_GpuMat()
gpu_image.upload(cpu_image)  # 8000px image

# Process 550 frames entirely on GPU
for frame in range(550):
    M = compute_affine_matrix(frame)  # zoom/pan params
    cropped = cv2.cuda.warpAffine(gpu_image, M, size)
    resized = cv2.cuda.resize(cropped, (1344, 756))
    # Frame stays in GPU memory or downloads for encoding

# Download only when needed for FFmpeg encoding
```

### Expected Performance

| Metric | Current (CPU) | OpenCV CUDA |
|--------|---------------|-------------|
| Scaling approach | Per-frame CPU | Per-frame GPU |
| Memory transfers | N/A | 2 per image (not per frame) |
| Speedup | 1x | 3-5x |
| GPU utilization | 1% | High |

**Hardware context:** nuca-systems has RTX 5080 with 10,240 CUDA cores, currently idle during video generation.

## Hybrid Pipeline

OpenCV CUDA replaces **only** the Ken Burns step. FFmpeg handles everything else:

| Step | Tool |
|------|------|
| Ken Burns (zoompan) | **OpenCV CUDA** |
| Transitions (xfade) | FFmpeg |
| Audio mixing | FFmpeg |
| Video encoding | FFmpeg NVENC |
| Container muxing | FFmpeg |

## Sources

- Perf profiling: `/tmp/perf_*.data` on nuca-systems (Dec 2024)
- Issue tracking: [#329 VideoGen Scaling Spike](https://github.com/DaveX2001/deliverable-tracking/issues/329)
- Related doc: [CPU Filter Bottleneck Analysis](cpu-filter-bottleneck-analysis.md)
- Related doc: [VideoGen Scaling Spike](videogen-scaling-spike.md)
