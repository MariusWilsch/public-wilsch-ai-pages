# v2 Wizard Architecture Snapshot
[[client-paul]]

---
publish: true
date: 2026-01-04
type: snapshot
status: may-change
source: rubber-duck session #380
---

> **Snapshot Notice:** This document captures architecture decisions from a specific point in time. Decisions may evolve as implementation progresses.

## 1. Data Flow Philosophy

- **v2 owns user data:** Frontend writes config directly to Supabase
- **v1 as backend service:** v2 calls v1 API for GPU processing (RunPod)
- **FDW bridge:** v1 reads Supabase tables directly via postgres_fdw - no v1 code changes

## 2. Step Structure (Data-Driven)

- **Step 3 renamed "Configuration":** Collects Voice + ImageStyle + VideoGenTemplate + Template
- **Timing constraint:** All config must be selected BEFORE its pipeline stage runs
- **Step 4 narrowed:** Now just image demo/preview - config selection moved to Step 3

## 3. v2 Frontend Features Status

| Feature | Exists | Path |
|---------|--------|------|
| channel-management | ✅ | `frontend/src/features/channel-management/` |
| create-video | ✅ | `frontend/src/features/create-video/` |
| image-style | ✅ | `frontend/src/features/image-style/` |
| queue | ✅ | `frontend/src/features/queue/` |
| voice | ✅ | `frontend/src/features/voice/` |
| Template | ❌ | Not built |
| VideoGenTemplate | ❌ | Not built |
| PromptConfiguration | ❌ | Form-only, not persisted |

## 4. Entity-to-Step Matrix

| Entity | Step 1 | Step 2 | Step 3 | Step 4 | Step 5 | Tier One |
|--------|:------:|:------:|:------:|:------:|:------:|:--------:|
| channels | ✓ | — | — | — | ✓ | ✅ IN |
| video_process | ✓ | ✓ | ✓ | ✓ | ✓ | ✅ IN |
| voices | — | — | ✓ | — | — | ✅ select |
| image_styles | ✓ | — | — | ✓ | — | ✅ IN |
| templates | ✓ | ✓ | ✓ | ✓ | — | ✅ select |
| video_gen_templates | ✓ | — | — | — | ✓ | ✅ select |
| prompt_configurations | ✓ | ✓ | — | — | — | ✅ optional |

## 5. Entity Status vs Tier One Scope

| Entity | In Supabase | v2 UI | Tier One Need |
|--------|-------------|-------|---------------|
| channels | ✅ | ✅ | Unlimited |
| video_process | ✅ | ✅ | Core workflow |
| voices | ✅ | ✅ | Select only (~16 predefined) |
| image_styles | ✅ | ✅ | Predefined + custom |
| templates | ❌ | ❌ | Select only (predefined) |
| video_gen_templates | ❌ | ❌ | Select only (predefined) |
| prompt_configurations | ❌ | Form only | Optional (use defaults) |

## 6. FDW Pattern

- **Direction:** v1 PostgreSQL reads FROM Supabase (not the reverse)
- **Connection:** Session pooler `aws-1-eu-central-1.pooler.supabase.com`, SSL required
- **Username format:** `postgres.{projectref}` (e.g., `postgres.owftrowgzjlkjjtraici`)
- **Validated:** Spike successful on local PostgreSQL

## 7. v2→v1 Integration

- **No FastAPI needed:** v1 Flask already has RunPod integration
- **Flow:** v2 frontend → v1 `/api/video/create` → returns `process_id`
- **ID mapping:** Store `{supabase_video_id, v1_process_id}` in Supabase for tracking

## 8. Issue Structure

- **#380 Update:** Step 4 = image demo only (config moved to Step 3)
- **New issue:** Step 3 Expansion - UI + Supabase tables for Template/VideoGenTemplate
- **New issue:** FDW + v2→v1 Integration - infrastructure connecting systems
- **Close #411:** LLM Migration not needed (v1 handles LLM via existing endpoints)

## 9. Key Architectural Decisions

**Autopilot Modes:**
- Both ON and OFF need the SAME entities - mode doesn't change dependencies
- Autopilot just changes orchestration, not data requirements

**Template Abstraction:**
- v1 uses Template indirection (Template → Voice, ImageStyle, etc.)
- v2 currently wizard-only (config stored directly on video_process)
- For Tier One: Introduce Template as "select only" presets

**UI Placement:**
- Template + VideoGenTemplate selection → Step 1 wizard (dropdown + dialog pattern)
- Reference implementation: Voice selection in Step 3 (`Step3Voice.tsx`, `VoiceSelectionDialog.tsx`)

**Database Sync Pattern:**
- Chose FDW over: dual-write, sync worker, CDC
- Reason: Zero v1 code changes, v1 queries unchanged, real-time reads

**No FastAPI Layer:**
- v1 Flask already has RunPod integration for TTS, ImageGen
- FastAPI would be unnecessary middleman for VideoCreate scope
- YouTube OAuth is out of scope for now

## References

- [Strangler Pattern ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/adr-strangler-pattern-sequencing)
- [Light Tier Scope](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/light-tier-v2-feature-scope)
- [Unified Script Step ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/adr-wizard-unified-script-step)
