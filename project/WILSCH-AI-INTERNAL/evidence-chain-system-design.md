---
publish: true
---

# Evidence Chain System for Non-Interactive Sessions

[[claude-code-sdk-agents]]

Design for a surface-agnostic evidence capture mechanism that replicates Session A (`/flag-for-improvement`) for SDK agent failures.

---

## Problem Statement

SDK agents (fireflies-webhook, board-sync, action-bot, email-sync, and 6 others) run autonomously in DaveX2001/deliverable-tracking/agents/. When they fail — wrong issue matching, duplicate creation, confidence gaps — the only capture mechanism is WhatsApp. This violates the Issue Lifecycle Router's foundational principle: "No floating context. Nothing lives outside the system."

The existing `/flag-for-improvement` workflow depends on an interactive Claude Code session where the AI that failed can introspect. SDK agents have no interactive session — their "conversation" is a Langfuse trace. Session A (failure capture) has no equivalent for non-interactive evidence.

The System Engineer position agreement (Standards 2-4) requires evidence at issue creation time, but provides no mechanism for evidence that lives in Langfuse rather than conversation JSONL files.

**Preconditions:**
- SDK agents are deployed and logging to Langfuse (8 of 10 agents)
- GitHub Issues is the dominant output surface (7 of 10 agents)
- `/flag-for-improvement` exists for interactive session evidence but not for agent evidence
- Langfuse trace URLs are stable, linkable evidence artifacts

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | When a user observes unexpected SDK agent behavior, evidence is captured and linked to the correct issue within the interaction — not lost on WhatsApp. |
| **Success** | A working end-to-end pipeline: `/flag` on GitHub → AI resolves Langfuse trace → user confirms via OpenClaw → evidence comment on agent's epic. |
| **Done test** | Can Marius flag an agent failure from a GitHub issue and have the evidence appear on the agent's improvement epic without WhatsApp? If yes → done. |

---

## Approach

### Part 1: Evidence Capture Pipeline

The core mechanism is surface-agnostic — the same operation regardless of where the user captures from.

```
GitHub:    /flag [observation] on issue
               ↓ webhook
OpenClaw:  AI: "I think this is about [agent]. Correct?"
           User: "Yes"
           AI: "Found trace [URL] showing [finding]. Link this?"
           User: "Yes"
               ↓
GitHub:    Evidence comment on agent's improvement epic
```

**Capture** happens on GitHub (low friction — you're already on the issue). **Discussion** happens in OpenClaw (natural chat — two touchpoints). **Evidence** lands on GitHub (agent's epic).

**Input:** Free-form text only. Agent name is always inferred (primary axis). Issue number and Langfuse trace are evidence artifacts — they get attached to the agent's epic, not the other way around.

**Two touchpoints:**
1. **Agent confirmation:** AI infers which agent from the text, user validates
2. **Trace confirmation:** AI proposes the relevant Langfuse trace, user validates

**Resolution path:**
1. Infer which agent from the observation text
2. Confirm agent with user (touchpoint 1)
3. Search that agent's recent Langfuse traces
4. If issue number provided → narrow traces to that issue
5. Propose the most relevant trace (touchpoint 2)
6. On confirmation → evidence comment on agent's epic

### Part 2: Agent-Centric Evidence Model

Evidence accumulates per AGENT, not per issue. Each SDK agent has a long-lived improvement epic in GitHub.

```
Epic: "Improve fireflies-webhook"
  ├── Comment: "Evidence from #601: [trace]. Created 4 dupes." (2026-02-18)
  ├── Comment: "Evidence from #389: [trace]. Confidence gap." (2026-02-20)
  └── Comment: "Evidence from #157: [trace]. Wrong match." (2026-02-22)
```

**Comments for evidence, sub-issues for action.** Sub-issues are created manually when the user reviews the epic and decides to act on a pattern. This separates fast capture (comments) from deliberate action (sub-issues).

**Why agent-centric:** Improving an agent requires the pattern across all its failures. The epic is the pattern container.

### Part 3: Hybrid Surface Architecture

| Surface | Role | Issue # available? |
|---------|------|-------------------|
| **GitHub Comments** | Capture (`/flag [observation]`) | Yes (implicit) |
| **OpenClaw** | Discussion + confirmation (two touchpoints) | Via webhook payload |

**Why hybrid:** GitHub provides frictionless capture (you're already on the issue). OpenClaw provides natural discussion (chat interface for confirmation). Each surface does what it's best at.

**Infrastructure:** Moltbot container (OpenClaw predecessor) already runs on WILSCH-AI-SERVER (port 18789). Likely upgradable to OpenClaw.

### Part 4: Evidence Resolution Engine

The AI searches Langfuse using filters extracted from the `/flag` input:

| Filter | Source |
|--------|--------|
| Agent name | Confirmed by user (touchpoint 1) |
| Issue number | From GitHub comment context (if available) |
| Time range | Recent by default |

**Undefined:** Langfuse access mechanism — community MCP server or custom. → [Discussion Topic 1](evidence-chain-meeting-agenda#1-langfuse-mcp-selection)

**Undefined:** Metadata reliability — is issueNumber consistently tagged? → [Discussion Topic 2](evidence-chain-meeting-agenda#2-search-strategy-and-metadata-reliability)

**Undefined:** Multiple match handling. → [Discussion Topic 3](evidence-chain-meeting-agenda#3-multiple-match-handling)

### Part 5: Design Phases

| Phase | What | Surfaces |
|-------|------|----------|
| **Phase 1: PULL** | On-demand evidence capture via `/flag` | GitHub + OpenClaw |
| **Phase 2: REVIEW** | Periodic agent activity digest | TBD |
| **Phase 3: Surface abstraction** | Formalize interface contract | All surfaces |

**Undefined:** Phase 2 REVIEW mechanism — possibly a UI with a list view, potentially using Claude Code `--sdk-url` for interactive chat over the digest. Needs more design. → [Discussion Topic 5](evidence-chain-meeting-agenda#5-review-mechanism-design)

**Undefined:** Surface abstraction layer (Phase 3). → [Discussion Topic 6](evidence-chain-meeting-agenda#6-surface-abstraction-layer)

---

## Source

- **Issue:** [#851: Evidence Chain System](https://github.com/DaveX2001/deliverable-tracking/issues/851)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/51421801-e902-49e7-b6ab-26bded59186f.jsonl
- **Related docs:**
  - [System Engineer Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/system-engineer-position-agreement-wilsch-ai-services)
  - [/improve-system Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture)
  - [Context Graph ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/context-graph-langfuse)
  - [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router)
  - [Fireflies Agent Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/fireflies-agent-capture-reliability-design)
