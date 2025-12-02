---
publish: true
date: 2025-12-02
---

# Serverless LLM Cold Start Research
[[testing-methodology]]

## 1. The Problem We Observed

Experienced ~3 minute cold starts on RunPod with vLLM (Qwen3-14B).

**Root cause breakdown** (from endpoint logs):

| Phase | Time |
|-------|------|
| Model load | ~7s |
| torch.compile | ~53s |
| CUDA graph capture | ~63s |
| Other init | ~77s |
| **Total** | **~200s** |

Key insight: Model caching only helps with ~7s. The vLLM warmup (~190s) runs every cold start.

## 2. Platform Comparison: RunPod vs Modal

| Aspect | RunPod FlashBoot | Modal GPU Snapshots |
|--------|------------------|---------------------|
| Mechanism | Keeps containers warm in RAM | Saves GPU state to disk (CRIU) |
| After idle | Resources may be reclaimed | Restores from disk |
| Traffic dependency | Yes | No |
| Captures vLLM warmup | No | Yes |
| Cold start (best) | 500ms-2s | 2-4s |
| Cold start (after idle) | Full (~200s) | ~5s |

**vLLM on Modal benchmark:** 45s → 5s cold start (Qwen2.5-0.5B)

### Sources
- [RunPod FlashBoot](https://www.runpod.io/blog/introducing-flashboot-serverless-cold-start)
- [RunPod Cached Models](https://docs.runpod.io/serverless/endpoints/model-caching)
- [Modal GPU Snapshots](https://modal.com/blog/gpu-mem-snapshots)
- [Modal vLLM Example](https://modal.com/docs/examples/vllm_inference)

## 3. Inference Engine Alternatives to vLLM

| Engine | Notes | Link |
|--------|-------|------|
| **SGLang** | 3.1x throughput vs vLLM, RadixAttention for KV cache reuse | [Docs](https://github.com/sgl-project/sglang) |
| **LMDeploy** | 1.8x throughput, simpler install | [Docs](https://github.com/InternLM/lmdeploy) |
| **TGI** | HuggingFace official, mature | [Docs](https://huggingface.co/docs/text-generation-inference) |

All available on Modal. SGLang and TGI also on RunPod.

### Sources
- [SGLang vs vLLM](https://www.runpod.io/blog/sglang-vs-vllm-kv-cache)
- [vLLM Alternatives Comparison](https://www.designveloper.com/blog/vllm-alternatives/)

## 4. Caching Layer: LMCache

**What it is:** KV cache reuse layer that works WITH inference engines (vLLM, SGLang).

**What it helps:** Repeated queries, multi-turn conversations (15x throughput boost reported).

**What it doesn't help:** Cold start - engine still needs to warm up first.

### Sources
- [LMCache Blog](https://blog.lmcache.ai/)
- [LMCache + vLLM Integration](https://blog.lmcache.ai/2025-05-16-release/)

## 5. Related

- Issue: DaveX2001/deliverable-tracking#9 (AWS vLLM deployment)
- See also: `aws-vs-runpod-comparison.md` in project/archibus-fm-assistant

---

*Research date: 2025-12-02*
