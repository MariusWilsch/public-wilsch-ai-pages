---
publish: true
---

# TTS Vertical Slices
[[client-paul]]

Vertical slice approach for migrating TTS generation from local GPU to RunPod serverless.

## Why Vertical Slices

The horizontal approach (building all layers at once) failed because when audio quality issues appeared, it was impossible to isolate whether the problem was in:
- Chunking logic
- RunPod handler
- Whisper validation
- Candidate selection
- Concatenation

Vertical slicing means: **one atomic change per slice, test after each**. If a slice breaks audio quality, you know exactly what caused it.

## The Pipeline (Reference)

```
Phase 1: Text Preparation (Flask - unchanged)
├─ Directory setup, locate transcript, preprocess text

Phase 2: Audio Generation
├─ Stage 2.1: Setup (model loads in handler)
├─ Stage 2.2: Chunking (local CPU)
├─ Stage 2.3: Generation ← THIS IS WHAT MOVES TO RUNPOD
└─ Stage 2.4: Concatenation (local CPU)

Phase 3: Post-Processing (Flask - unchanged)
├─ Verify output, save metadata, build segments
```

## Stage 1: Flask + Handler (Sequential)

Keep Flask code structure. Replace only the `model.generate()` call with a RunPod handler call. Handler absorbs more functionality with each slice.

| Slice | Handler Responsibility | Flask Responsibility | Test |
|-------|----------------------|---------------------|------|
| 1 | `model.generate()` only | Candidate loop, validation, selection, concatenation | AssemblyAI transcribe → compare |
| 2 | Generate N candidates, return all | Validate each locally, select best locally | Same |
| 3 | Generate N + Whisper validate each | Select best from scores | Same |
| 4 | Generate N + Validate + Select best | Just receive 1 audio per chunk, concatenate | Same |

**After Slice 4:** Handler is complete. It accepts text, returns best audio. Flask just loops through chunks sequentially and concatenates results.

## Stage 2: FastAPI (Parallel Dispatch)

Handler doesn't change. Only the **calling code** moves from Flask to FastAPI to enable async parallel dispatch.

| Slice | Change | Why |
|-------|--------|-----|
| 5 | Move dispatch logic to FastAPI (still sequential) | Prove FastAPI integration works |
| 6 | Change to parallel dispatch (`asyncio.gather`) | Performance gain: 10-15x for typical chapter |

**After Slice 6:** Full pipeline works with parallel chunk processing.

## What Stays Constant Throughout

- **Phase 1** (text preparation): Always in Flask, never changes
- **Phase 3** (post-processing): Always in Flask, never changes
- **Stage 2.2** (chunking): Stays local CPU, logic unchanged
- **Stage 2.4** (concatenation): Stays local CPU, logic unchanged

Only Stage 2.3 (generation) migrates, and even then the LOGIC stays the same - just WHERE it runs changes.

## Validation Protocol

After every slice:

1. Generate audio with new slice code
2. Run `validate_tts.py` (AssemblyAI transcription)
3. Compare transcript to original text
4. If content loss, stuttering, or garbling → debug before next slice
5. If clean → proceed to next slice

The validation script saves transcript to `<audio>.transcript.txt` for comparison.

## Handler Simplification Note

The existing RunPod handler (whisper-v2 image) has all features baked in. For vertical slicing, the handler should be rebuilt starting simple:

- **Slice 1 handler:** Just `model.generate()`, return audio
- **Slice 2 handler:** Add N candidate loop
- **Slice 3 handler:** Add Whisper validation
- **Slice 4 handler:** Add selection logic

This ensures each handler change can be tested in isolation.

## References

- [TTS Pipeline Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/tts-pipeline-architecture) - Full pipeline documentation
- [TTS Pipeline AFTER RunPod](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/tts-pipeline-after-runpod) - Target architecture
- Issue #198 (closed) - Documents why horizontal approach failed
