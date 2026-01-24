---
publish: true
---

# ADR: Parallel Paths for ARCHIBUS AI FM Automation
[[client-archibus]]

## Status
**Created** | Date: 2026-01-24

## Context
- AI-assisted FM automation spans both broad framework capabilities and specific quick wins
- Framework work (#373) depends on external schema understanding (Ryan meeting)
- Marketing needs demo content for client engagement now
- Quick wins (#591 CCI codes, #605 Equipment codes) have no schema dependency
- Different stakeholders drive different tracks (capability vs marketing)

## Decision
We will separate framework development from marketing quick wins as **parallel tracks** with independent dependencies and timelines.

**Framework Path (#373 under #267):**
- Broad capabilities requiring schema understanding
- Proceeds when external dependency resolves

**Quick Wins Path (#591, #605 under #594):**
- Marketing-driven demos
- No schema dependency - proceed immediately
- Workflow: Spec-design → David spec-implement → SDK Agent

## Consequences

### ✅ Positive
- Quick wins proceed without waiting for framework blockers
- Marketing has demo content for client engagement
- Framework work continues at its own pace when ready
- Clear ownership: marketing owns quick wins timeline

### ❌ Negative
- Two coordination surfaces instead of unified track
- Potential divergence in implementation approaches

### ⚪ Neutral
- Different implementation paths (framework vs SDK agents)
- Requires explicit communication when paths reconnect

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Unified track** | Blocks all progress on schema dependency |
| **Quick wins only** | Loses broader framework capabilities |
| **Sequential (framework first)** | Delays marketing demos indefinitely |

---

**Key Resources:**
- #267: Value Discovery Methodology (framework parent)
- #373: AI Bulk Data Entry Prototype (framework path)
- #594: YouTube Marketing Funnel (quick wins parent)
- #591, #605: Current quick win examples
