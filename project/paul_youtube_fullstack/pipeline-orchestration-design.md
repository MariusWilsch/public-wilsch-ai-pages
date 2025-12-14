---
publish: true
---

# Pipeline Orchestration Design
[[client-paul]]

FastAPI orchestration layer for video generation pipeline. Decouples CPU orchestration from GPU services using strangler pattern.

## 1. Purpose

- FastAPI orchestration layer for video generation pipeline
- Decouples CPU orchestration from GPU services
- Strangler pattern: wire existing services, swap later

## 2. Architecture

- FastAPI intercepts queue layer (not individual steps)
- Calls: TTS (RunPod), ImageGen (Runware), Video (existing Express)
- Entry point: `POST /jobs` replaces Flask `POST /api/video/create`

```
┌─────────────────────────────────────────────────────────────────┐
│ FastAPI (NEW)                                                   │
│ ├── POST /jobs (create + queue)                                 │
│ ├── Orchestration (dispatch steps)                              │
│ └── Queue management                                            │
└───────────────────┬─────────────────────────────────────────────┘
                    │ calls
                    ▼
┌─────────────────────────────────────────────────────────────────┐
│ Existing Services                                               │
│ ├── TTS → RunPod (#198)                                         │
│ ├── ImageGen → Runware (concurrency pattern TBD)                │
│ └── Video → Flask/Express (until #233)                          │
└─────────────────────────────────────────────────────────────────┘
```

## 3. Pipeline Steps

| Step | Service | Notes |
|------|---------|-------|
| TTS | RunPod (#198) | Wire handler, parallel chunks handled by handler |
| ImageGen | Runware API | Investigate best concurrency pattern |
| Video | Existing Express | Wire existing (no change until #233) |

**Sequential gate:** `segments.json` must complete before ImageGen starts (see #123 research).

## 4. Dependencies

- **Requires:** #235 (Schema), #230 (Auth), #198 (TTS integrated)
- **Enables:** #233 swap-in when ready
- **Research:** #123 (batch-wait decision, segments.json gate)

```
#235 ✓ → #230 ✓ → #244 (Orchestration) → #233 swaps in → #238
```

## 5. Strangler Compliance

- No rewriting services - only wiring
- Respect existing constraints (segments.json sequential gate)
- Flask continues handling non-pipeline routes
- Swap Express → FastAPI video when #233 completes

## Related Documents

- [TTS Pipeline Architecture](tts-pipeline-architecture.md) - Current TTS flow
- [TTS Pipeline AFTER RunPod](tts-pipeline-after-runpod.md) - Target TTS architecture
- Issue #244 - Implementation tracking
- Issue #123 - Pipeline research
