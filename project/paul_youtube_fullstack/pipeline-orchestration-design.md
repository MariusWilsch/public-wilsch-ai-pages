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

## 6. Migration Phases (TTS-First)

Strangler pattern: migrate one service at a time, validate, then expand.

| Phase | Issue | Scope | Done When |
|-------|-------|-------|-----------|
| 1. TTS | [#244-1](https://github.com/DaveX2001/deliverable-tracking/issues/245) | FastAPI TTS endpoints + Flask integration | Flask calls FastAPI for TTS, old tts.py deleted |
| 2. ImageGen | [#244-2](https://github.com/DaveX2001/deliverable-tracking/issues/246) | FastAPI ImageGen endpoints + Flask integration | Flask calls FastAPI for images, old code deleted |
| 3. Video | [#244-3](https://github.com/DaveX2001/deliverable-tracking/issues/248) | Express wiring + full orchestration | Full pipeline via FastAPI |

**Why TTS first:**
- Simplest service (single external call to RunPod)
- Validates webhook architecture before expanding
- Lowest risk - image/video unchanged during validation

## 7. Integration Pattern (Transition State)

During migration, Flask becomes a thin orchestrator that delegates to FastAPI:

```
┌─────────────────────────────────────────────────────────────────┐
│ Flask (Transition State)                                        │
│ ├── Receives /api/video/create                                  │
│ ├── Step 1-2: Handles locally (transcript, script)              │
│ ├── Step 3: Calls FastAPI /tts/batch ────────────────┐         │
│ ├── Step 4: Calls FastAPI /imagegen/batch (Phase 2)  │         │
│ └── Step 5-7: Handles locally (video, upload)        │         │
└──────────────────────────────────────────────────────┼─────────┘
                                                       │
                                                       ▼
┌─────────────────────────────────────────────────────────────────┐
│ FastAPI (Growing)                                               │
│ ├── POST /tts/batch → RunPod (webhook)                          │
│ ├── POST /tts/callback ← RunPod                                 │
│ ├── GET /tts/batch/{id} → status                                │
│ └── (Phase 2: /imagegen/* endpoints)                            │
└─────────────────────────────────────────────────────────────────┘
```

**Flow for TTS Phase:**
1. Flask step 3 calls `POST /tts/batch` with chunks
2. FastAPI submits to RunPod with webhook URL
3. RunPod calls `POST /tts/callback` on completion
4. Flask polls `GET /tts/batch/{id}` until done
5. Flask retrieves audio and continues to step 4

**After all phases:** Flask orchestration logic deleted, FastAPI owns `POST /jobs`.

## Related Documents

- [TTS Pipeline Architecture](tts-pipeline-architecture.md) - Current TTS flow
- [TTS Pipeline AFTER RunPod](tts-pipeline-after-runpod.md) - Target TTS architecture
- [RunPod Webhook Pattern](runpod-webhook-pattern.md) - Webhook implementation details
- Issue #244 - Implementation tracking
- Issue #123 - Pipeline research
