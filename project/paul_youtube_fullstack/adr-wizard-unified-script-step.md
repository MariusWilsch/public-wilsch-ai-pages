# ADR: Unified Script Step in v2 Wizard
[[paul-youtube-fullstack]]

publish: true

## Status
**Accepted** - 2026-01-03

## Context

The v1 Create Video wizard uses **conditional step visibility**:
- "Raw Transcript" step only appears for Video URL mode
- "Voice Configuration" step combines LLM output display + voice selection + speed

This creates UX confusion:
- Steps appear/disappear based on mode selection
- Different step counts between modes (5 vs 4)
- "Voice Configuration" is misleadingly named (contains LLM output editing)

## Decision

v2 will use a **unified step architecture** with mode-adaptive content:

### Step Structure (Always 5 Steps)

| Step | Name | Content |
|------|------|---------|
| 1 | Setup | Source type selection + input (URL/file/description) |
| 2 | **Script** | Mode-adaptive content editing (unified) |
| 3 | **Voice** | Voice selection + speed only |
| 4 | Footage | Image style selection |
| 5 | Preview | Final result |

### Step 2 "Script" - Mode Adaptation

**Video URL mode:**
- Shows raw YouTube transcript in editor
- "Enable AI Enhancement" toggle
- When enhanced: shows processed version (can compare with raw)

**Custom Script mode:**
- File upload or paste text area
- Optional "Enhance with AI" toggle
- Shows uploaded/pasted content

**Title Generation mode:**
- Shows description input + AI-generated script
- "Regenerate" option for different versions

### Key Changes from v1

| Aspect | v1 | v2 |
|--------|-----|-----|
| Raw Transcript step | Conditional (Video URL only) | Always visible as "Script" |
| LLM output display | In Voice Config (Step 3) | In Script (Step 2) |
| Voice step content | LLM output + voice + speed | Voice + speed ONLY |
| Step visibility | Conditional based on mode | Always 5 steps |
| Autopilot flow | Skip wizard → /downloads | Summary Review → Generate |

### Express Path Decision

When `autopilot=true`, show **Summary Review screen** before generation (not skip directly to downloads).

**Why:** Users need transparency about what will be auto-configured. Even experienced users benefit from seeing settings before committing. Industry pattern: "Express Mode with Smart Defaults" (Stripe Express Checkout, Amazon One-Click show confirmation before action).

**Flow:**
- Manual Mode: Step 1 → 2 → 3 → 4 → 5
- Express Mode: Step 1 → Summary Review → Generate → Queue

### Skip State Decision

Skipped steps remain **visible** (not hidden) with distinct visual state and explanation.

**Why:** Users learn full flow even in simple modes. Mental model transfers across configurations. Pattern: Spatial consistency - navigation never changes shape.

**Visual states:**
- Active: filled (current step)
- Completed: outline (done, clickable)
- Skipped: dashed outline + explanation (not applicable, still clickable to see why)

## Consequences

### Positive
- Consistent user experience across all modes
- Clear semantic naming (Script = your content, Voice = audio settings)
- Users learn one flow that applies to all modes
- No "disappearing steps" confusion

### Negative
- Diverges from v1 behavior (migration consideration)
- Step 2 content varies significantly between modes
- More complex Step 2 component implementation

### Impact on Step Responsibilities

**Step 2 (Script):**
- Owns all content editing (raw + enhanced)
- Handles mode-adaptive switching

**Step 3 (Voice):**
- Audio settings ONLY (voice selection + speed)
- No content display or editing

## References

- [Light Tier Scope](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/light-tier-v2-feature-scope)
- [Strangler Pattern ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/adr-strangler-pattern-sequencing)
