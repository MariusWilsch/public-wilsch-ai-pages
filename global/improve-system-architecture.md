---
publish: true
---

[[claude-code-architecture]]

# /improve-system Architecture

Implementation blueprint for the instruction artifact improvement ecosystem.

## Overview

Two commands orchestrate a three-session workflow for fixing instruction artifacts (skills, commands, hooks, protocol).

| Command | Session | Purpose |
|---------|---------|---------|
| `/flag-for-improvement` | A (Project) | Capture failure: introspect + create issue |
| `/improve-system` | B (soloforce) | Fix workflow: load context + guide phases + verify gate |

## Session Flow

```
Session A (Project): Notice issue → /flag-for-improvement → Issue created
                                                              ↓
Session B (soloforce): /improve-system → Pick issue → Worktree → Extract convo
                                                              ↓
                       Load background (ADRs, hooks, persuasion) → Rubber-duck
                                                              ↓
                       Diagnose artifact type (command/skill/hook/protocol)
                                                              ↓
                       Clarity phases → Execute → PRE-COMMIT PAUSE
                                                              ↓
Session A2 (Project): Verify fix → Report back
                                                              ↓
Session B (cont): Confirm → Push worktree to main → Commit with issue ref
```

## Session A: Failure Capture

| Step | Action | Notes |
|------|--------|-------|
| 1 | User notices issue | - |
| 2 | `/flag-for-improvement "description"` | - |
| 3 | AI introspects: "Why did I do that?" | Traces reasoning chain |
| 4 | Preview issue before creating | Like deliverable-tracking pattern |
| 5 | Create issue | Includes: conversation path from `~/.claude/projects/` |

**Issue contains:**
- What failed (user description)
- AI reasoning trace (introspection output)
- Suspected artifact (command/skill/protocol/hook)
- Conversation path for later extraction
- Label: `CLAUDE-CODE-IMPROVEMENTS`

## Session B: Diagnosis & Fix

| Step | Action | Notes |
|------|--------|-------|
| 1 | `/improve-system` (no args) | Lists all `CLAUDE-CODE-IMPROVEMENTS` issues |
| 2 | User picks issue | - |
| 3 | Create worktree | Via worktree skill - isolated branch |
| 4 | Extract conversation | Script pulls from path in issue |
| 5 | Load background context | ADRs, persuasion techniques, hooks docs |
| 6 | `/rubber-duck` diagnosis | Includes artifact type decision |
| 7 | Clarity phases | Requirements → Implementation → Evaluation |
| 8 | Execute changes | In worktree |
| 9 | PRE-COMMIT PAUSE | "Verify in new project session" |

## Artifact Type Diagnostic

Part of `/rubber-duck` diagnosis - the fix depends on artifact type:

| Artifact Type | Enforcement | When to Use |
|---------------|-------------|-------------|
| **Command** | Instruction-based | AI follows written guidance |
| **Skill** | Instruction-based | Reusable workflow/domain knowledge |
| **Hook** | Automatic | Enforcement that doesn't rely on AI compliance |
| **Protocol** | Foundational | Core behavioral patterns |

**Example:** "AI didn't use AskUserQuestion" could be:
- Command fix: Strengthen `/rubber-duck` instruction
- Hook fix: Stop hook that blocks plain-text questions

## Session A2: Verification

| Step | Action | Notes |
|------|--------|-------|
| 1 | New project session | Fresh context |
| 2 | Trigger same scenario | - |
| 3 | Observe: fixed or not? | - |
| 4 | Return to Session B | - |

## Session B (cont): Finalize

| Step | Action | Notes |
|------|--------|-------|
| 1 | User confirms verification | - |
| 2 | Push worktree to main | Only after verification passes |
| 3 | Commit with issue reference | Auto-links to tracking |

## Component Inventory

### Existing (Ready to Use)

| Component | Location | Purpose |
|-----------|----------|---------|
| `/rubber-duck` | `~/.claude/commands/` | Externalize thinking, diagnose |
| `/requirements-clarity` | `~/.claude/commands/` | WHAT to build |
| `/implementation-clarity` | `~/.claude/commands/` | HOW to build |
| `/evaluation-clarity` | `~/.claude/commands/` | WHEN it's done |
| `deliverable-tracking` skill | `~/.claude/skills/` | Issue creation pattern |
| `worktree` skill | `~/.claude/skills/` | Isolated development |
| `skill-creator` skill | `~/.claude/skills/` | Skill modifications |
| `extract_conversation.py` | `soloforce/research/scripts/` | Conversation → JSONL |
| `CLAUDE-CODE-IMPROVEMENTS` label | deliverable-tracking repo | Issue tagging |

### To Build

| Component | Location | Purpose |
|-----------|----------|---------|
| `/flag-for-improvement` | `~/.claude/commands/` | Session A entry point |
| `/improve-system` | `~/.claude/commands/` | Session B orchestrator |

## Background Context for /improve-system

Documents to load during Session B:

- Hook documentation (what hooks exist, capabilities)
- Persuasion techniques ADRs
- Protocol structure (CLAUDE.md sections)
- Artifact type guidance (this document)

## Introspection Reference

Anthropic research on triggering introspection: [Introspection Research](https://www.anthropic.com/research/introspection)

Key finding: Direct instruction ("think about your reasoning") can trigger introspection, but it's unreliable (~20% success rate). Framing around consequences may help.

For `/flag-for-improvement`: When AI introspects "why did I do that?", expect potential confabulation. Session B verification catches inaccurate self-diagnosis.

---

**Related:**
- ADR: [Three-Session Model](three-session-instruction-artifact-improvement.md)
- Implementation tracking: [Issue #49](https://github.com/DaveX2001/deliverable-tracking/issues/49)
