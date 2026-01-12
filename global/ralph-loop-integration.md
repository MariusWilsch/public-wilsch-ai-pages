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
- [Anthropic: Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

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

### H3: Sanity checks = Ralph's backpressure

Sanity checks ("does it exist/run?") during impl ARE Ralph's backpressure mechanism:
- Database: Migration applied? Columns exist?
- Backend: Endpoint responds? No crashes?
- Frontend: Component renders? Build passes?

This is NOT an extension - it's Ralph's core. We preserve it in prompt_impl.md.

### H4: AC verification as separate session

AC verification ("does it behave correctly?") runs in SEPARATE session to prevent confirmation bias.
- Ralph relies on tests during build only
- We extend with prompt_verify.md for Given-When-Then verification
- Results go to verification.jsonl

This IS an extension of Ralph - adding the three-session model's bias prevention.

### H5: tracking.md combines prd.json + specs

Single file combines Ralph's multiple artifacts:
- DoD section = task list (Ralph's prd.json)
- AC section = specifications (Ralph's specs/*.md)

More efficient than multiple files. Same information, better locality.

### H6: Parent issue as scope container

Human creates parent issue with phases (the "starting stone"). Ralph never designs from zero:
- Parent provides 80% clarity on scope
- Design mode creates sub-issues FROM parent phases
- Matches Anthropic's initializer pattern

## Future End State

If all hypotheses prove true, the end state is:

```
Human creates parent issue (one-time scope definition)
           │
           ▼
    ┌──────────────────────────────────────────────┐
    │              RALPH LOOP                       │
    │                                              │
    │  ┌─────────┐    ┌─────────┐    ┌─────────┐  │
    │  │ design  │───▶│  impl   │───▶│ verify  │  │
    │  │         │    │         │    │         │  │
    │  │ Creates │    │ Builds  │    │ Runs    │  │
    │  │ sub-    │    │ with    │    │ ACs     │  │
    │  │ issue + │    │ sanity  │    │         │  │
    │  │ DoD/AC  │    │ checks  │    │         │  │
    │  └─────────┘    └─────────┘    └────┬────┘  │
    │       ▲                             │       │
    │       │         ┌───────────────────┘       │
    │       │         │                           │
    │       │    pass │  fail                     │
    │       │         ▼                           │
    │  next phase◄────┴────►back to impl          │
    │                                              │
    └──────────────────────────────────────────────┘
           │
           ▼
    All phases complete → Parent issue closed
```

**Human role:** Create parent issue, monitor progress, intervene only on blockers.

**Ralph role:** Design sub-issues, implement with backpressure, verify with ACs, auto-chain between modes.

## How to Extend

Add hypotheses as new patterns emerge. Keep stateless.

## Related Documents

- [three-session-model.md](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model)
- [ac-dod-framework.md](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework)
