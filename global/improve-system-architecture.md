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
Session B (soloforce): /improve-system → FIFO + related hint → User picks
                                                              ↓
                       Worktree → Extract convo → Layer 1 context
                                                              ↓
                       /rubber-duck → Diagnose artifact type
                                                              ↓
                       Clarity phases → manage-artifact skill (Layer 2) → Execute
                                                              ↓
                       PRE-COMMIT PAUSE → "Verify in project session"
                                                              ↓
Session C (Project): Verify fix → Report back
                                                              ↓
                       ↙ If failed: back to rubber-duck with new context
Session B (cont): Confirm → PR via worktree → Merge (auto-closes issue)
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

### Issue Selection (FIFO + Related Hint)

| Step | Action | Notes |
|------|--------|-------|
| 1 | `/improve-system` | Fetches oldest open issue (FIFO default) |
| 2 | Semantic search for related | Search title+body for similar issues |
| 3 | Present list to user | "Oldest: #49. Related: #137, #133 (step-skipping)" |
| 4 | User picks issue | Can choose oldest or any from list |

### Setup & Context (Progressive Disclosure)

| Step | Action | Notes |
|------|--------|-------|
| 5 | Create worktree | Via worktree skill - isolated branch |
| 6 | Extract conversation | Script pulls from path in issue |
| 7 | **Layer 1 context** | Brief artifact summary (see below) |
| 8 | `/rubber-duck` diagnosis | Outputs artifact type decision |

**Layer 1 (before diagnosis, ~100 tokens):**
```
Artifact Types:
- Command (instruction-based) → fix with /stage
- Skill (reusable workflow) → fix with skill-creator
- Hook (automatic enforcement) → fix via settings.json
- Protocol (core patterns) → fix via CLAUDE.md edit
```

### Fix Workflow

| Step | Action | Notes |
|------|--------|-------|
| 9 | `/requirements-clarity` | WHAT to fix |
| 10 | `/implementation-clarity` | HOW to fix → `manage-artifact` skill discovered |
| 11 | `/evaluation-clarity` | Success criteria |
| 12 | Execute + **Layer 2 context** | `manage-artifact` skill loads relevant reference |
| 13 | PRE-COMMIT PAUSE | "Verify in new project session" |

### Failed Verification Handling

If user returns from Session C saying "didn't work":
- Return to Step 8 (rubber-duck) with NEW conversation context
- Iterate diagnosis and fix with updated understanding
- Issue stays open until fix verified

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

## Session C: Verification

| Step | Action | Notes |
|------|--------|-------|
| 1 | New project session | Fresh context |
| 2 | Trigger same scenario | - |
| 3 | Observe: fixed or not? | - |
| 4 | Return to Session B | - |

**Current Implementation:** `manage-artifact` skill has a monitoring loop that polls Session C conversation every 30 seconds, watching for success/failure signals in real-time.

## Session B (cont): Finalize

| Step | Action | Notes |
|------|--------|-------|
| 1 | User confirms verification | - |
| 2 | Create PR via worktree skill | PR description includes `Fixes #X` |
| 3 | Merge PR | GitHub auto-closes issue on merge |

**Issue Closure:** Via PR workflow with `Fixes DaveX2001/deliverable-tracking#X` in PR description. GitHub auto-closes the issue when PR merges. This is handled by the worktree skill.

## Component Inventory

| Component | Purpose |
|-----------|---------|
| `/flag-for-improvement` | Session A entry point |
| `/improve-system` | Session B orchestrator |
| `/rubber-duck` | Externalize thinking, diagnose |
| Clarity commands | `/requirements`, `/implementation`, `/evaluation` |
| `manage-artifact` skill | Layer 2 context loader + monitoring loop |
| `worktree` skill | Isolated development branches |
| `skill-creator` skill | Skill modifications |
| `conversation-reader` skill | Conversation extraction |

## Introspection Reference

Anthropic research on triggering introspection: [Introspection Research](https://www.anthropic.com/research/introspection)

Key finding: Direct instruction ("think about your reasoning") can trigger introspection, but it's unreliable (~20% success rate). Framing around consequences may help.

For `/flag-for-improvement`: When AI introspects "why did I do that?", expect potential confabulation. Session B verification catches inaccurate self-diagnosis.

---

**Related:**
- ADR: [Three-Session Model](three-session-instruction-artifact-improvement.md)
- Implementation tracking: [Issue #49](https://github.com/DaveX2001/deliverable-tracking/issues/49)
