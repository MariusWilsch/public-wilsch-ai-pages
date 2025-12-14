---
publish: true
---

# RunPod Webhook Pattern for FastAPI
[[client-paul]]

Production pattern for handling RunPod serverless jobs with webhook callbacks. Target architecture for FastAPI rebuild (issue #122, subissue #244).

## When to Use

| Context | Pattern |
|---------|---------|
| Flask (current) | Polling `/status` - works locally, simpler |
| FastAPI (production) | Webhooks - efficient, no polling waste |

## Architecture Overview

```
┌─────────┐     ┌──────────────┐     ┌─────────┐
│ Client  │────▶│   FastAPI    │────▶│ RunPod  │
└─────────┘     └──────────────┘     └─────────┘
     │                │                   │
     │  1. Submit     │   2. Submit       │
     │     batch      │      jobs +       │
     │                │      webhook URL  │
     │                │                   │
     │                │◀──────────────────│
     │                │  3. Webhook POSTs │
     │                │     (N× per job)  │
     │                │                   │
     │  4. Poll       │                   │
     │     status     │                   │
     │◀───────────────│                   │
     │                │                   │
     │  5. Get final  │                   │
     │     result     │                   │
└─────────────────────┴───────────────────┘
```

## Key Endpoints

### 1. Submit Batch

```python
@router.post("/tts/batch")
async def submit_batch(request: TTSBatchRequest):
    """Submit multiple TTS chunks for parallel processing."""
    job_ids = []

    for chunk in request.chunks:
        response = await runpod_client.submit({
            "input": {
                "text": chunk.text,
                "audio_prompt_url": chunk.voice_url,
                "n_candidates": 3,
                "whisper_threshold": 85
            },
            "webhook": f"{settings.BASE_URL}/api/tts/callback"
        })
        job_ids.append(response["id"])

    batch_id = create_batch(job_ids)
    return {"batch_id": batch_id, "job_count": len(job_ids)}
```

### 2. Webhook Callback

```python
@router.post("/tts/callback")
async def webhook_callback(payload: RunPodWebhookPayload):
    """Receives job completion notifications from RunPod."""
    job_id = payload.id
    status = payload.status

    if status == "COMPLETED":
        audio_b64 = payload.output["audio_base64"]
        await store_result(job_id, audio_b64)
    elif status == "FAILED":
        await mark_failed(job_id, payload.output.get("error"))

    # IMPORTANT: Always return 200 to acknowledge
    return {"acknowledged": True}
```

### 3. Check Batch Status

```python
@router.get("/tts/batch/{batch_id}")
async def get_batch_status(batch_id: str):
    """Client polls this endpoint (not RunPod directly)."""
    batch = await get_batch(batch_id)

    return {
        "batch_id": batch_id,
        "total": batch.total_jobs,
        "completed": batch.completed_count,
        "failed": batch.failed_count,
        "status": "COMPLETED" if batch.is_done else "IN_PROGRESS",
        "result_url": batch.result_url if batch.is_done else None
    }
```

## RunPod API Reference

### Submit Job with Webhook

```
POST https://api.runpod.ai/v2/{ENDPOINT_ID}/run
{
  "input": {...},
  "webhook": "https://your-api.com/callback"
}
```

### Cancel Job

```
POST https://api.runpod.ai/v2/{ENDPOINT_ID}/cancel/{JOB_ID}
```

### Purge Queue (bulk cancel pending)

```
POST https://api.runpod.ai/v2/{ENDPOINT_ID}/purge-queue
```

## Error Handling Strategy

1. **Submit all jobs** via `/run` with webhook URL
2. **Track job IDs** in database/Redis
3. **Webhooks notify** on completion/failure
4. **On failure detection:**
   - `/purge-queue` to clear pending jobs
   - `/cancel/{id}` for in-progress jobs
   - Mark batch as failed

## Webhook Behavior

| Aspect | Value |
|--------|-------|
| Retry attempts | 3 total (2 retries) |
| Retry delay | 10 seconds |
| Payload | Same as `/status` response |
| Result expiry | 30 minutes after completion |

## Schemas

```python
class RunPodWebhookPayload(BaseModel):
    id: str
    status: Literal["COMPLETED", "FAILED", "CANCELLED", "TIMED_OUT"]
    delayTime: int  # ms in queue
    executionTime: int  # ms running
    output: Optional[dict] = None

class TTSBatchRequest(BaseModel):
    chunks: list[TTSChunk]
    voice_url: str

class TTSChunk(BaseModel):
    text: str
    index: int
```

## Why Webhooks Over Polling

| Aspect | Polling | Webhooks |
|--------|---------|----------|
| API calls | 50 jobs × N polls = hundreds | 50 callbacks (RunPod initiates) |
| Latency | Polling interval delay | Instant notification |
| Client coupling | Client knows about RunPod | Client only talks to your API |
| Complexity | Simpler | Requires public endpoint |

## Related

- [TTS Pipeline Architecture](tts-pipeline-architecture.md)
- [TTS Pipeline AFTER RunPod](tts-pipeline-after-runpod.md)
- Issue #122: FastAPI rebuild
- Issue #244: Webhook pattern implementation
