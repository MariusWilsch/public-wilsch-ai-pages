---
publish: true
---

# CCI Board Structure - Position Interaction Model
[[wilsch-ai-internal]]

Design doc for how the Claude Code Improvements board is structured around positions as the organizing axis, replacing flat issue sprawl with position epics and observation-based evidence collection.

---

## Problem Statement

The CCI board lacks an organizing principle. Issues are created flat — one per observation — without hierarchy or position association. Over 4 triage sessions (210 issues), this produced sprawl where related observations scattered across standalone issues. Patterns are invisible. Prioritization by position is impossible. The current tooling (flag-for-improvement) always creates new issues, reinforcing the flat structure.

**Preconditions:**
- Multiple positions (Developer, System Engineer, JA) submit to the same CCI board
- Observations arrive continuously from sessions, transcripts, and reviews
- No structural rules exist for grouping or routing observations
- flag-for-improvement has no "comment on existing issue" mode

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Every CCI observation has a position home and implicit state through the release cycle |
| **Success** | New observations route to position epics via AI-suggested routing. Existing labeled issues converted to observation comments. flag-for-improvement supports comment-on-epic mode. |
| **Done test** | Can I write a meeting agenda with open design questions about CCI board structure? If NO → design is complete |

---

## Approach

### 1. Organizing Axis

Deliverable-tracking organizes by **project** — each client gets a label, issues group under it. CCI organizes by **position** — each E-Myth position gets a long-lived epic, observations group under it.

Position is the organizing axis of CCI. Every observation, every improvement, every release ties back to which position it serves. The triage sessions (4 sessions, 210 issues) created the position labels that make this possible: `traceline/developer`, `traceline/system-engineer`, `internal/developer`, `internal/system-engineer`, `internal/ja`.

**Undefined:** The label taxonomy uses a position × scope decomposition (traceline vs internal). Whether this maps 1:1 to position epics or requires further refinement is unresolved. → Meeting agenda topic.

### 2. Observation Model

Observations are evidence captured as comments on position epics. The model adapts the surface-agnostic pattern from the Evidence Chain System (#851):

```
Free-form observation → AI resolves evidence + suggests position epic → User confirms → Comment posted
```

Observations are not issues — they have no state. They are context that makes behavior problems visible to AI. The System Engineer PA (Standard #2) established the principle: "Evidence must be captured and connected to an issue at flagging time." The position epic IS that issue.

An observation has two parts:
- **Context (the hook):** What was observed — the entry point that tells the System Engineer where to look in the source. Always problem-based, never solution-based. The source conversation contains noise; the hook cuts through it by naming the specific behavior problem. Without a problem-framed hook, the source is a pond with no indication of what fish to catch.
- **Source (the pond):** Conversation path, transcript URL, or commit link — the full evidence where the AI pinpoints the problematic behavior and, through thinking traces and tool usage patterns, diagnoses WHY it happened. The source gives the AI a first-person view of its own reasoning — something the human cannot express from their third-person perspective.

This format was validated live during the extraction session — an output format observation was posted to CCI #589 as a comment instead of creating a new issue.

**Undefined:** The observation format should not be fully free-form. A structured "how to write an observation" guide needs to be embodied into the capture tooling (flag-for-improvement or equivalent). → Meeting agenda topic.

### 3. Two-Level Hierarchy

The board has two structural levels:

| Level | Type | Lifecycle | Purpose |
|-------|------|-----------|---------|
| **Level 1** | Position epic | Permanent (never closes) | Collects observations as they arrive |
| **Level 2** | Release epic (sub-issue) | Time-bound (created → worked → closed) | Groups observations selected for a work cycle |

**Flow:**
1. Observations accumulate on the position epic (Level 1) as comments
2. When ready to work: create a release epic as a sub-issue of the position epic (Level 2)
3. Transfer relevant observations from position epic to release epic
4. Work the release epic to completion, then close it
5. Remove transferred observations from the position epic

State tracking is implicit — no marking mechanism needed:
- **In position epic** = unaddressed (backlog)
- **In release epic** = being worked on (in-progress)
- **Release epic closed** = done

**Accumulation threshold:** A release epic is not created for a single observation. Observations accumulate on the position epic like atmospheric pressure building. Each new observation adds context that makes the others more actionable — the AI gets a richer evidence base for diagnosis. When enough observations cluster around a coherent concern, the pressure crosses a threshold and a release epic forms to discharge them. The more observations, the better the fix — because explaining behavior problems is hard for humans, but pattern-matching across multiple evidence sources is where AI excels.

**Core principle — output vs. behavior:** AI text output is non-deterministic (the same greeting may use seven different phrasings). AI behavior is deterministic (the act of greeting always happens). If wrong behavior is 100% consistent, the inverse must hold: a fix can make right behavior 100% consistent. Observations capture behavior, not output. Multiple observations of the same wrong behavior confirm the pattern is behavioral — not output variance. This is why accumulation matters: one observation might be noise, but a cluster of observations pointing at the same behavior is a confirmed fix target. This is the bread and butter of the improve-system cycle.

Artifact grouping (which commands, skills, or protocols are affected) happens during release planning, not as a structural level. This keeps the hierarchy flat at two levels.

**Undefined:** The exact mechanism for transferring observations from position epic to release epic (copy comment content? link to original? delete original?) needs definition through usage. → Meeting agenda topic.

### 4. Routing

When an observation is captured, routing determines which position epic receives it.

**Mechanism:** AI suggests, user confirms.
- The AI knows the active session's position (from onboarding context)
- Default route = current position's epic
- AI can suggest a different position epic based on observation content
- User confirms or overrides

This replaces the current model where flag-for-improvement creates standalone issues with no position association. The routing decision is lightweight — a handful of position epics, not 200+ open issues to search through.

### 5. Tooling Evolution

The primary tooling change is flag-for-improvement. Currently it always creates a new standalone issue. Under the new model it needs two modes:

1. **Observe** — Post an observation comment on a position epic (default mode)
2. **Create** — Create a new standalone issue (for genuinely new concerns that don't belong to any existing position epic)

The AI handles mode selection as part of routing (Part 4). When the AI identifies a position epic match, it defaults to Observe mode. When no match exists, it falls back to Create mode.

**PA standard (new):** When a new position is created, a corresponding CCI position epic must be created immediately. This is a standard in the Position Agreement, not a separate tooling concern.

**Migration:** Existing labeled CCI issues are converted to observation comments on their position epics in a one-time pass. This is manual work driven by this conversation, not automated tooling.

---

## Source

- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/530036f4-94ab-419c-8100-745707068c77.jsonl
- **System Engineer PA:** https://mariuswilsch.github.io/public-wilsch-ai-pages/global/system-engineer-position-agreement-wilsch-ai-services
- **Evidence Chain System:** DaveX2001/deliverable-tracking#851
- **JA Lifecycle Violations:** DaveX2001/claude-code-improvements#589
- **Flag-for-improvement gap:** DaveX2001/claude-code-improvements#363
- **CCI Triage Sessions:** DaveX2001/deliverable-tracking#819 (comments contain 4 session summaries)
- **Live validation:** CCI #589 comment (observation format success case)
