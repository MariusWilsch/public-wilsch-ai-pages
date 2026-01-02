---
publish: true
---

# ADR: Context Graph Implementation via Langfuse

[[claude-code-architecture]]

**Date:** 2026-01-02
**Status:** Proposed
**Issue:** [#154 Context Graph Evolution](https://github.com/DaveX2001/claude-code-improvements/issues/154)

---

## Context

We have SDK agents (fireflies-webhook, board-sync, action-bot) that make decisions autonomously. Currently they log decisions to Langfuse, but only capture WHAT happened (State Clock), not the reasoning behind decisions (Event Clock).

Marius documented the AC Verification Decision Trace schema showing how to capture reasoning as searchable precedent:
- https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-verification-decision-trace

The key insight: **State Clock** tells you what's true now. **Event Clock** tells you what happened + WHY + reasoning. Without the Event Clock, future sessions can't learn from past decisions.

---

## Current Architecture (WRITE only)

```
┌──────────────────┐         ┌──────────────────┐
│     AGENTS       │ ──────► │    LANGFUSE      │
│                  │  write  │  (Context Graph) │
│ • fireflies      │         │                  │
│ • board-sync     │         │  Stores:         │
│ • action-bot     │         │  • what          │
│                  │         │  • why           │
│                  │         │  • issueNumber   │
│                  │         │  • source        │
└──────────────────┘         └──────────────────┘
                                     │
                                     ▼
                              Marius can see
                              what happened
```

### Current DecisionTrace Schema

```typescript
interface DecisionTrace {
  agent: string;           // ✅ Have
  action: string;          // ✅ Have
  what: string;            // ✅ Have
  why: string;             // ✅ Have
  issueNumber?: number;    // ✅ Have
  source?: { type, id };   // ✅ Have
  alternatives?: [];       // ✅ Have (but not used!)
}
```

### What's MISSING for Context Graph

| Field | Status | Purpose |
|-------|--------|---------|
| `client` | ❌ Missing | Query "all IITR decisions" |
| `interpretation` | ❌ Missing | Per-step reasoning (Event Clock) |
| `context_snapshot` | ❌ Missing | What info was available when deciding |
| `entity` | ❌ Missing | People/companies mentioned |
| `trace_type` | ❌ Missing | ac_verification, email_match, etc. |

---

## Target Architecture (READ + WRITE)

```
                         ┌─────────────────────┐
                         │      LANGFUSE       │
                         │   (Context Graph)   │
                         │                     │
                         │  • All Decisions    │
                         │  • With Reasoning   │
                         │  • Searchable       │
                         └─────────────────────┘
                                 ▲     │
                          write  │     │  read
                                 │     ▼
         ┌───────────────────────┴─────┴───────────────────────┐
         │                                                     │
┌────────┴────────┐                              ┌─────────────┴─────────────┐
│     AGENTS      │                              │       CLAUDE CODE         │
│                 │                              │                           │
│ fireflies ──────┼── "Matched #157 because..."  │  Before deciding:         │
│ board-sync ─────┼── "Blocked because..."       │  → Query Langfuse         │
│ action-bot ─────┼── "Closed because..."        │  → "How did we handle     │
│                 │                              │     this before?"         │
│                 │                              │  → Precedent as context   │
└─────────────────┘                              │                           │
                                                 │  After error:             │
                                                 │  → "What went wrong?"     │
                                                 │  → Learn from trace       │
                                                 └───────────────────────────┘
```

---

## Decision

Implement Context Graph in 3 phases using Langfuse as the storage layer.

### Phase 1: Enhanced Metadata (NOW)

Extend `DecisionTrace` schema in `agents/shared/langfuse.ts`:

```typescript
interface DecisionTrace {
  // Existing fields
  agent: string;
  action: string;
  what: string;
  why: string;
  issueNumber?: number;
  source?: { type: "email" | "transcript" | "comment"; id: string };

  // NEW: Context Graph fields
  client?: string;                    // "IITR", "UWI", "PAUL-FACELESS-YT"
  interpretation?: string;            // Per-decision reasoning (Event Clock)
  alternatives?: Array<{
    issue: number;
    rejected: string;                 // WHY rejected
  }>;
  context_snapshot?: {
    issues_considered?: number[];     // What issues were known
    keywords?: string[];              // What keywords triggered
    confidence?: number;              // Decision confidence
  };
  trace_type?: string;                // "match_issue", "create_issue", "ac_verification"
}
```

**Example - Before (State Clock):**
```javascript
logDecision("fireflies-webhook", "match_issue", {
  what: "Matched to #157",
  why: "Title match",
});
```

**Example - After (Event Clock):**
```javascript
logDecision("fireflies-webhook", "match_issue", {
  what: "Matched to #157",
  why: "Title match",
  client: "IITR",
  interpretation: "Chose #157 because exact title match outweighs recency of #160",
  alternatives: [
    { issue: 160, rejected: "Lower confidence (40%) despite being more recent" }
  ],
  context_snapshot: {
    issues_considered: [157, 160, 163],
    keywords: ["navigation", "assets"],
    confidence: 0.85
  },
  trace_type: "match_issue"
});
```

### Phase 2: Precedent Queries (NEXT)

Build **Langfuse MCP** so Claude Code can READ from Context Graph:

```typescript
// Claude Code could then do:
mcp__langfuse__query_traces({
  filter: { client: "IITR", action: "match_issue" },
  limit: 5
})

// Returns precedent:
[
  { what: "Matched #157", interpretation: "...", alternatives: [...] },
  { what: "Matched #142", interpretation: "...", alternatives: [...] }
]
```

**This enables:**
- "How did we handle similar IITR transcripts before?"
- "What decisions were made for issue #157?"
- "Show all cases where alternatives were considered"

### Phase 3: Cross-System Linking (FUTURE)

Connect data across systems:

```
Email ID ──► Issue Number ──► Transcript Timestamp
                │
                ▼
        Unified Timeline per Client
```

---

## Langfuse Sessions Feature

Langfuse Sessions (https://langfuse.com/docs/observability/features/sessions) can group traces:

```
session_id = issue_number OR date-based
           ↓
All decisions for Issue #377 → one session
```

This enables timeline view of all agent actions for a specific issue.

---

## How Claude Code Uses Context Graph (Memory)

| Phase | What Happens |
|-------|--------------|
| **1. Agent works** | fireflies-webhook matches transcript → logs to Langfuse |
| **2. User asks** | "Why was #157 matched?" |
| **3. Claude queries Langfuse** | Fetches trace with interpretation, alternatives, context |
| **4. Claude explains** | "Agent chose #157 because X, not #160 because Y" |
| **5. Error found?** | "context_snapshot shows: Issue #163 wasn't known" |
| **6. Fix** | Update agent, better decisions logged |

**This IS Claude's memory** - queryable precedent for future decisions.

---

## Components Status

| Component | Status | Purpose |
|-----------|--------|---------|
| Agents → Langfuse (write) | ✅ Done | Log decisions |
| Enhanced Metadata Schema | 🚧 Phase 1 | client, interpretation, etc. |
| Update fireflies-webhook | 🚧 Phase 1 | First agent with rich traces |
| Update board-sync | 🚧 Phase 1 | Second agent |
| Update action-bot | 🚧 Phase 1 | Third agent |
| Langfuse MCP (read) | ❌ Phase 2 | Claude can query precedent |
| Cross-system linking | ❌ Phase 3 | Email ↔ Issue ↔ Transcript |

---

## References

- [AC Verification Decision Trace (Marius)](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-verification-decision-trace)
- [Langfuse Sessions Docs](https://langfuse.com/docs/observability/features/sessions)
- [Issue #154 - Context Graph Evolution](https://github.com/DaveX2001/claude-code-improvements/issues/154)
- [Issue #155 - LangFuse Self-Hosted](https://github.com/DaveX2001/claude-code-improvements/issues/155)
- [Context Graph Research - How to Build](https://x.com/akoratana/status/1872755487498289488)

---

## Next Steps

1. **Approve this ADR** - Marius + David alignment
2. **Implement Phase 1** - Schema + Agent updates
3. **Test in Langfuse** - Verify rich traces appear
4. **Plan Phase 2** - Langfuse MCP design
