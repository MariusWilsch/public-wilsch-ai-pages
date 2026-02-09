---
publish: true
---

# Design Doc & Meeting Agenda Methodology
[[life-vision]]

How design docs and meeting agendas are created at Wilsch AI Services. Two halves of one system: design docs capture what IS defined, meeting agendas capture what ISN'T. Defines stable component structures, thinking functions, and the workflows for extracting and formalizing knowledge.

---

## Problem Statement

Design docs produced by AI are structurally inconsistent — different section headers, different organization, different depth each time. The user has to re-read and re-orient every new document because there's no predictable structure. Additionally, without a structured component list, AI proposes everything at once, making validation overwhelming rather than focused.

**Preconditions:**
- AI-assisted knowledge extraction from meeting transcripts
- User has conceptual (not explicit) recall of meeting content
- Output must be a publishable hippocampus document
- Multiple stakeholders need alignment (user, client, developer)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A repeatable design doc structure that works across all projects — stable components with project-specific content |
| **Success** | User gives AI the component list. AI proposes understanding per component. User validates one-by-one. Each component is either Defined (write it) or Undefined (meeting agenda topic). When all Defined → design is done |
| **Done test** | "Can I write a meeting agenda with open design questions?" → If NO → design doc is complete |

---

## Approach

### Two-Phase Workflow

Knowledge extraction happens in two phases with distinct cognitive functions:

| Phase | Function | Mode | Trigger |
|-------|----------|------|---------|
| **Rubber duck** (prerequisite) | Discover understanding you didn't know you had | Divergent — free-form spitballing, follow threads | Session starts with transcript/topic |
| **Design doc** (this system) | Capture and verify that understanding | Convergent — structure into components, fill gaps | User-initiated: "I want to get specific now" |

**Why both phases are needed:** The rubber duck phase produces insights through following threads freely (e.g., "Step 2 mapping isn't just documentation — it's the transformation rule"). If you skip to components immediately, you only capture what you already know. The divergent phase discovers what you didn't know you knew.

**Transition signal:** User feels ready to get specific. Not a rule or threshold — a judgment call when divergent thinking stops producing new territory.

### Stable Components

Four components appear in every design doc. Section headers are always the same — this creates habit and consistent thinking across projects. Content is always project-specific because every design is different.

| Component | Thinking Function | Mandatory Elements |
|-----------|------------------|-------------------|
| **Problem Statement** | Scope crystallization — what IS / ISN'T the problem | Preconditions: the conditions under which this problem exists. Mandatory because every problem has them, and they themselves need validation. |
| **Success Definition** | Three functions: (1) "Done" boundary — high-level end state, above ACs, requires human judgment. (2) Human judgment anchor — evaluation isn't automatable, someone must assess. (3) Client alignment surface — holdable agreement between parties. | — |
| **Approach** | Two functions: (1) AI-driven decomposition — AI breaks vague conceptual understanding into concrete named parts, user validates. (2) Conceptual ordering — arranges parts so each builds on understanding from the previous one (comprehension flow, not implementation steps). | Content is always project-specific |
| **Source** | JIT retrieval anchor — ensures next session can find and fetch sources directly without searching. Infrastructure, not thinking. | Findable links: conversation paths, transcript URLs, commit hashes. Not descriptions. |

No optional components by default. Only added if user explicitly requests them.

### Meta Functions (All Components Share)

Every component serves three functions beyond its unique thinking purpose:

1. **AI proposal format** — gives AI a structure to embody its understanding. AI always drafts first.
2. **Validation surface** — user sees AI's articulation and can immediately say "yes" or "not like that." Without seeing it written, validation is impossible.
3. **Defined/undefined test** — binary state per component. Can this be written? If not → Undefined → becomes a meeting agenda topic.

### Component States

Each component has a binary state: **Defined** or **Undefined**.

- **Defined:** Content is clear, no remaining questions. Written in full.
- **Undefined:** Content cannot be defined with current understanding. The uncertainty description lives in the **meeting agenda**, NOT in the design doc.

The design doc only contains what IS defined. Clean separation of responsibilities.

### Trust Gradient

AI always leads by proposing first. The difference across components is the user's ability to judge:

| Component | User's Judgment | AI's Recall Advantage |
|-----------|----------------|----------------------|
| **Problem Statement** | Precise — "I know this IS the problem" | Low — user knows it clearly |
| **Success Definition** | Directional — "this feels like done" | Medium — user has a sense, AI structures |
| **Approach** | Instinctive — "yes/no, can't articulate why" | High — user knows conceptually, AI has explicit transcript recall |

The gap between conceptual and explicit knowledge widens from Problem → Approach. This is where AI's decomposition becomes most valuable.

### Component-by-Component Rhythm

1. AI drafts ONE component AND asks questions about its own uncertainties within that component
2. User validates the draft AND resolves AI's questions
3. Next component
4. Repeat until all four are done

User controls pace. Small compressed drafts in chat → expanded in published document. User reads published doc via Speechify (sees + hears simultaneously). Multiple review rounds are normal and expected.

---

## Meeting Agenda

The other half of the system. When a design doc component is **Undefined**, its uncertainty moves to a meeting agenda. The meeting fills the gap. The design doc gets updated. The agenda is not a checklist — it's a container for facilitated discovery.

### Stable Structure

Every meeting agenda answers five questions:

| # | Question | Section | Thinking Function |
|---|----------|---------|-------------------|
| 1 | **WHY** are we meeting? | Meeting Goal | Architect's prediction of what can be resolved — pre-commitment to what the meeting should produce |
| 2 | **WHAT context** does the attendee need? | Pre-Read (optional) | "What do they need to arrive with the same context I have?" — usually links to the design doc, sometimes a contract, sometimes nothing if the agenda is self-contained |
| 3 | **WHAT unknowns** are we resolving? | Discussion Topics | Decomposition of undefined areas — AI proposes, principles constrain the format, content is project-specific |
| 4 | **WHAT outcome?** | Embedded in Meeting Goal | No separate section — the Goal already defines what success looks like |
| 5 | **HOW** does the meeting work? | Meeting Format | Type, duration, expectations |

Meeting Goal and Discussion Topics are always present. Pre-Read and Meeting Format are contextual.

### Discussion Topic Principles

| Principle | Rule |
|-----------|------|
| **Statements, not questions** | Don't interrogate — open space. Questions lead. Statements invite. |
| **Starting points, not exhaustive** | "Starting points for discussion, not limited to these." Topics open the conversation, don't constrain it. |
| **First-person perspective** | Write for the reader (participant), not third-person observation |
| **No facilitation meta-commentary** | Direct quotes as starters, not "conversation prompt:" framing |
| **Process-general framing** | "What challenges exist in determining hierarchy?" not "What challenges will AI face?" |
| **Stimulus-based facilitation** | Present something concrete → let participants react → infer the process |
| **No "Next Steps" section** | Implied by meeting format |

### The Design Doc ↔ Meeting Agenda Flow

```
Design doc (Undefined components) → Meeting agenda (Discussion Topics)
                                      ↓
Meeting agenda (Pre-Read) → links back to design doc
                                      ↓
Meeting fills gaps → Design doc updated (Undefined → Defined)
```

The attendee reads the design doc before the meeting. The discussion topics are the gaps in that doc. The meeting fills the gaps. The architect updates the doc. When no Undefined components remain → design is complete.

---

## Source

- **Design doc session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/f819eaff-5acf-4089-9b47-c6966afc3766.jsonl
- **Meeting agenda session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/456f6c80-6ae1-47de-ac09-d8cb75772042.jsonl
- **Evidence conversations:**
  - `39c514f1` — Chain 1B Step 3 design doc (strongest component-by-component evidence: "Let's decide on the component structure, then rock through each one")
  - `038e99d2` — YouTube Marketing Funnel (design doc + meeting agenda workflow: "Let's go through each section one by one again")
  - `77fa2cc5` — Meeting agenda friction points (perspective, verification, format preferences)
- **Meeting agendas analyzed:** [Mujahid - Asset Code](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/mujahid-meeting-agenda-asset-code-hosting), [Miguel/Ian - Hierarchy](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/miguel-ian-meeting-agenda-hierarchy-understanding), [Rein - Background Data](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-background-data), [Rein - Step 3 API](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/ryan-meeting-agenda-step3-insertion-api), [All-Hands Prioritization](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/all-hands-ai-work-track-prioritization), [Bulk Entry Spike](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/bulk-entry-spike-agenda)
- **CCI:** [#395](https://github.com/DaveX2001/claude-code-improvements/issues/395) — Design doc workflow (consolidates hippocampus improvements)
- **Design docs analyzed:** [Asset Code Generator](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/asset-code-generator-design), [Chain 1B Bulk Entry](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design), [Chain 1B Step 3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design), [Step 2 Schema Mapping](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-2-schema-mapping-design), [YouTube Marketing Funnel](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/youtube-marketing-funnel-execution-design)
- **Framework:** Michael Gerber, *The E-Myth Revisited*, Chapters 10, 15; [MG-0080 Operations Manual Guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-operations-manual-guide)
