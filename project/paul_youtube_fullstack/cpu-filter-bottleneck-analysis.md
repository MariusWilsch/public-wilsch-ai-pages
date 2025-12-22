---
publish: true
---

# CPU Filter Bottleneck Analysis
[[client-paul]]

## 1. Current State

### Pipeline Overview
- FFmpeg-based slideshow generation: static images → narrated video
- Output specs: 1-6 hour videos, 1080p/30fps, H.264/MP4, 300+ images per video
- Encoding: Uses NVENC (GPU accelerated)
- **Bottleneck location:** CPU filter processing, not encoding

### Filter Inventory

| Filter | Purpose | CUDA Alternative |
|--------|---------|------------------|
| **scale** | Image/video resizing | scale_cuda, scale_npp |
| **overlay** | Logo + dim layer compositing | overlay_cuda |
| **colorkey** | Black mask removal | chromakey_cuda |
| zoompan | Ken Burns effect | None |
| xfade | Transitions | None |
| subtitles | Captions | None |
| fps/trim/setpts | Timing control | None |

**Code location:** `paul_template_generation/backend/src/generator/generateSlideshow.ts`

---

## 2. Quick Wins

Filters with drop-in CUDA replacements:

### scale → scale_cuda / scale_npp
- **Impact:** HIGH - used on every image/video input
- **Swap:** Replace `scale=...` with `scale_cuda=...` or `scale_npp=...`

### overlay → overlay_cuda
- **Impact:** MEDIUM - 1-3 overlays per video
- **Swap:** Replace `overlay=...` with `overlay_cuda=...`

### colorkey → chromakey_cuda
- **Impact:** LOW - only when blackMask enabled
- **Swap:** Replace `colorkey=black:0.1:0.1` with `chromakey_cuda=0x000000:0.1:0.1`

### Pipeline with CUDA filters
```
GPU Decode → GPU Filters (scale_cuda, overlay_cuda) → CPU Filters (zoompan, xfade) → GPU Encode
           ↑ hwupload_cuda                          ↑ hwdownload required          ↑ NVENC
```

---

## 3. Remaining Bottlenecks

### Why zoompan and xfade cannot be GPU-accelerated

**Zoompan (Ken Burns):**
- Creates zoom + pan animation on static images over time
- Fundamentally single-threaded (confirmed from FFmpeg source - no SLICE_THREADS flag)
- No GPU alternative exists anywhere: FFmpeg, DaVinci Resolve, Premiere, After Effects all CPU-calculate
- Per-frame geometric transformation with sub-pixel interpolation is the expensive operation

**Xfade (Transitions):**
- Blends pixels between two video streams (fade, wipe, dissolve)
- CPU-only by neglect, not necessity - blend math is trivially parallelizable
- No FFmpeg CUDA implementation exists; no one prioritized building it
- Technically feasible to GPU-accelerate, just not built

**Subtitles:**
- Text rendering with fonts requires libass (CPU library)
- Forces hwdownload→CPU→hwupload when mixing with GPU filters

### Measured Impact
- GPU utilization during render: ~2% (idle, starved for frames)
- CPU utilization: ~158% (3-4 cores, bottleneck)
- Phases 1-5 (pre-FFmpeg): ~70ms total - negligible
- Phase 6 (FFmpeg): ~33 min for 6hr video

---

## 4. Options for True Bottlenecks

### 4a. Local Optimizations
- **Batch parallelization:** Run multiple FFmpeg instances (slides 1-100, 101-200, etc.), merge results
- **Pre-render caching:** Generate Ken Burns clips per image once, store as video, reuse
- **Accept CPU cost:** Scale horizontally with more processing capacity

### 4b. Cloud API Solutions
| API | Ken Burns | Control | Max Duration | Cost/min |
|-----|-----------|---------|--------------|----------|
| Shotstack | Preset-based | Limited | 3 hours | $0.20-0.30 |
| Creatomate | Full keyframe | Full | Unclear | ~$0.32 |
| Remotion Lambda | Code-based | Full | ~2 hours | $0.01-0.02 |

### 4c. What Production Teams Do
- Large-scale (100+ videos/month): Cloud APIs - speed/consistency beats local optimization
- Mid-scale (10-30/month): FFmpeg + pre-scaling + batching on multi-core CPUs
- Key technique: Pre-scale images to 8000px before zoompan (eliminates jitter)

---

## 5. Key Insights

- Quick wins exist but won't solve the core bottleneck (zoompan/xfade)
- GPU acceleration in professional software only helps final encoding, not filter computation
- The trade-off triangle: Control ↔ Speed ↔ Cost (pick two)
- Zoompan limitation is algorithmic (sequential frame dependency), not configuration
- No cloud API perfectly replicates exact Ken Burns parameters + transitions + 6hr duration

