---
publish: true
date: 2026-01-12
status: HYPOTHESIS - Ideas to verify through implementation
source: Rubber-duck session exploring Ralph integration
---

# Ralph Loop Integration: Ideas & Hypotheses

[[ralph-integration]]

## Overview

Exploring how Ralph autonomous coding loop integrates with three-session model.

## Source References

- [Medium: Claude Code + Ralph](https://medium.com/coding-nexus/claude-code-ralph-how-i-built-an-ai-that-ships-production-code-while-i-sleep-b59c9e1e7f8a)
- [Playbook: ghuntley/how-to-ralph-wiggum](https://github.com/ghuntley/how-to-ralph-wiggum)

## Concept Mapping

| Ralph Concept | Three-Session Equivalent | Notes |
|---------------|-------------------------|-------|
| loop.sh | Outer orchestration | Mode-agnostic |
| prompt.md | Session behavior | One per mode |
| prd.json | tracking.md | DoD + AC |
| progress.txt | AGENTS.md learnings | Combined with ops |

## Hypotheses (To Verify)

### H1: Three prompts = Three sessions

The three-session model (Design → Implementation → Verification) maps to three prompt modes:
- prompt_design.md (interactive)
- prompt_impl.md (autonomous)
- prompt_verify.md (autonomous)

Loop stays the same. Prompts define behavior.

### H2: Orchestration evolution

**Current:** Manual mode switching between sessions. Human runs `./loop.sh impl`, monitors, then runs `./loop.sh verify`.

**Future:** Auto-chaining. Loop detects impl complete → switches to verify → if fail → back to impl. Three sessions, one orchestration layer.

## How to Extend

Add hypotheses as new patterns emerge. Keep stateless.

## Related Documents

- [three-session-model.md](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model)
- [ac-dod-framework.md](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework)
