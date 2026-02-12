---
publish: true
---

# Design Doc & Meeting Agenda Methodology
[[life-vision]]

How design docs and meeting agendas are created at Wilsch AI Services. Two halves of one system: design docs capture what IS defined, meeting agendas capture what ISN'T. Defines stable component structures, thinking functions, and the workflows for extracting and formalizing knowledge.

---

## Problem Statement

Design docs produced by AI are structurally inconsistent — different section headers, different organization, different depth each time. The user has to re-read and re-orient every new document because there's no predictable structure. Additionally, without a structured component list, AI proposes everything at once, making validation overwhelming rather than focused.

The creation problem (structural inconsistency) was solved by stable components and component-by-component rhythm. A sequential problem emerged: once a design doc exists with Approach parts at various states, there is no formalized process for iteratively deepening each part toward full definition. Without systematic extraction, sessions can degrade into correction loops — AI produces outputs reactively, errors are caught mid-execution, and session time is spent on corrections instead of progression. More fundamentally, there is no reliable way to extract knowledge from the user, transcripts, or data sources into the design doc. Without a repeatable extraction process, each session spends energy figuring out HOW to deepen instead of focusing on the project content itself. Additionally, without a mechanism to present unknowns one at a time, the user is overwhelmed by parallel information — unable to focus questions through transcripts or data when facing a wall of undifferentiated ambiguity.

**Preconditions:**
- AI-assisted knowledge extraction from meeting transcripts
- User has conceptual (not explicit) recall of meeting content
- Output must be a publishable hippocampus document
- Multiple stakeholders need alignment (user, client, developer)
- Transcripts exist as source material for iterative extraction

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A repeatable design doc structure that works across all projects — stable components with project-specific content. A repeatable extraction process that deepens Approach parts session by session. |
| **Success** | *Creation:* All four components exist with content. The Approach has concrete named parts with conceptual ordering — this is the structure that extraction deepens. Problem Statement and Success Definition may evolve, but the Approach parts are the primary working surface. *Extraction:* AI surfaces numbered uncertainties from one Approach part. User and AI resolve one-by-one against transcripts, data, and user knowledge. One part, fully processed, one session. |
| **Done test** | *Doc-level:* "Can I write a meeting agenda with open design questions?" → If NO → design doc is complete. *Creation:* Four components exist, Approach has named parts. *Extraction:* SCOPE through ASSESS completes in one session for one part — session-atomic. |

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
2. **Validation surface** — user sees AI's articulation and can judge: "this feels right" or "not yet." Without seeing it written, validation is impossible. No formal state labels — user judgment is the mechanism.

### Component States

No formal state tracking. Stateful labels get stale quickly and cause more harm than help. The user judges whether a component or part feels sufficiently defined — this is the irreplaceable human contribution. AI structures and presents; the human looks at it and knows "this feels right" or "not yet." This division is the methodology's core: AI's value is recall and structure, the human's value is judgment. No state machine needed — the human IS the state machine. Multiple passes are normal. The user decides when to stop.

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

### Extraction Pass

The component-by-component rhythm creates the design doc. The extraction pass deepens it. One Approach part per session. Session-atomic — the full cycle completes before the session ends.

**Process:**

| Step | Name | Definition | Output |
|------|------|-----------|--------|
| 1 | **SCOPE** | Pick one Approach part. User judgment. That part is the session boundary. | "I am working on Part X" |
| 2 | **SURFACE** | AI produces numbered uncertainties (questions + hypotheses) from the scoped part text. No transcript reading at this stage. User validates the list. | Numbered list. List = SURFACE done. |
| 3 | **RESOLVE** | Chisel ambiguity one item at a time. AI self-routes each uncertainty before asking the user. User controls pacing — do not advance without explicit "next." Lightweight dependency ordering (conceptual building). Source attribution per resolution: Fireflies link + search anchor words. | Each item gets a disposition + source |
| 4 | **UPDATE** | AI proposes transition ("All items resolved. Ready to write?"), user confirms. Then: diff-based, section-by-section, approval-gated writing into the design doc — scoped part and any other components improved by the resolutions. Inline **Undefined** markers for flagged items link to meeting agenda. Meeting agenda created/updated after design doc changes, also component-by-component. Per-element source attribution inline (ideal) + session reference in Source section (minimum). Secondary validation surface: diff shows AI's understanding, user skims for instinctive judgment, new micro-decisions emerge from seeing text in context. | Updated design doc + meeting agenda if flagged items exist |
| 5 | **ASSESS** | Publish-verify-review. AI runs `verify_publish.sh`, opens commit link + published doc URL. User reads published doc (Speechify: hear + see). Improve loop: user gives feedback on wording → AI adjusts → repeat until satisfied. Session ends with explicit re-entry check: "Another extraction pass needed on any part?" | Published doc approved + re-entry decision |

**Self-Routing in RESOLVE:**

| Before asking user | AI checks | Tool | If found |
|---|---|---|---|
| Is this in **transcripts**? | Mine transcripts (chunked read) | Transcript mining skill *(TBD — `context: fork`, reads JSONL in chunks)* | Present for validation: "transcript says X — still valid?" |
| Is this in **data artifacts**? | Read data files | Standard file read / agents | Present for validation |
| Neither → **user decision** | — | AskUserQuestion | Evidence-informed question |
| User says "I don't know either" | — | — | Flag → meeting agenda |

**Design constraint:** Session-atomic. If the full cycle can't complete in one session, the scope was too broad or SURFACE produced too many uncertainties. The answer is tighter scoping, not carry-forward.

**SURFACE output example** *(from Archibus #373, elements 4-5-6):*

```
Part 4: Create Location-Based Assets
1. Floor All placement — where does building-wide equipment attach?
2. Multi-level emptiness — what if both room AND floor are empty?
3. Creation trigger — one asset per unique value at each level?
[...]

Part 5: Build Nested JSON
1. Field naming — PascalCase (ImportTemplate) vs lowercase (Rein's example)?
2. Country name → ID mapping — who resolves this?
[...]

Part 6: Insert via API + Backpressure
1. Error response format — undefined until API is built
[...]
```

*The part IS the categorization. Uncertainties come from reading the part text. Transcripts are not read during SURFACE.*

**UPDATE in practice** *(from AVO Soßen Sourcing #513, Part 4 extraction):*

UPDATE is not transcription — it's a secondary validation surface. The diff shows AI's understanding of the resolutions; the user skims and reacts instinctively ("makes sense" / "doesn't"). New micro-decisions about wording and formatting emerge from seeing the text in context — these cannot surface during RESOLVE because the document text doesn't exist yet during RESOLVE.

**Rhythm:**
1. AI proposes transition: "All items resolved. Ready to write into the doc?"
2. User confirms → AI loads hippocampus skill with design doc workflow
3. AI presents diff for ONE section (component or approach part)
4. User skims: `Apply` / `Adjust` / provides feedback
5. If feedback → AI revises diff → re-proposes
6. If Apply → AI writes the edit
7. Next section — repeat until all changes written
8. Inline **Undefined:** markers placed at end of sections for flagged items, linked to meeting agenda
9. Meeting agenda created/updated (also component-by-component with approval gates)
10. Source section updated with current session reference

**Diff format covers both modes:**
- **Edit existing content** → `-` (removed) and `+` (added) — shows what changed
- **Add new content** → all `+` — shows what's new, still skimmable

**Dual value of the diff:**
1. Shows the change — what's being written into the doc
2. Shows AI's understanding — how AI interpreted the resolutions. If AI understood correctly, the diff feels right. If not, user catches it. Mismatches become learning signals.

**Live example — Success Definition update (AVO, 3 review rounds):**

Round 1: AI proposed table-format diff → user asked about line breaks in tables.
Round 2: AI revised to list format → user added "otherwise adjust" to Done test.
Round 3: AI incorporated addition → user said "Apply."

```
# Before (table):
-| **Done test** | Query each recipe → partner appears in Top 5. If yes → validated. |

# After (list, with user addition):
+**Done test:** Query each recipe → partner appears in Top 5.
+If yes → baseline validated. Otherwise → adjust weights and iterate.
+A negative finding is also a valid POC result.
```

Three rounds on one section is normal. The format change (table → list) and content addition ("otherwise adjust") both emerged during UPDATE — neither existed during RESOLVE.

*Source: `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-AVO__poc/67641e8a-a998-4c2c-b7b6-67f7191bc2f4.jsonl`*

**ASSESS in practice** *(publish-verify-review loop):*

ASSESS is not a separate judgment — it's the validation surface shift from in-chat diffs to the published document. The user reads the real artifact, not a preview.

**Rhythm:**
1. AI runs `verify_publish.sh` — confirms doc is published
2. AI opens commit link (diff view) + published doc URL
3. User reads via Speechify (hear + see simultaneously)
4. User gives feedback on wording/writing → AI adjusts → re-publish → repeat
5. When satisfied: AI asks "Another extraction pass needed on any part?"
   - **Yes** → note which part, scope in next session
   - **No** → session complete

Multiple extraction passes on the same part are normal. Re-entry happens naturally when the user revisits the doc and feels "this part needs more work." The explicit question makes the decision visible rather than implicit.

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

**Creation methodology:**
- **Design doc session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/f819eaff-5acf-4089-9b47-c6966afc3766.jsonl
- **Meeting agenda session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/456f6c80-6ae1-47de-ac09-d8cb75772042.jsonl
- **Evidence conversations:**
  - `39c514f1` — Chain 1B Step 3 design doc (strongest component-by-component evidence: "Let's decide on the component structure, then rock through each one")
  - `038e99d2` — YouTube Marketing Funnel (design doc + meeting agenda workflow: "Let's go through each section one by one again")
  - `77fa2cc5` — Meeting agenda friction points (perspective, verification, format preferences)

**Extraction pass methodology:**
- **Rubber-duck session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/6269c598-82a4-49fe-892d-52ba74662ea8.jsonl
- **SURFACE in practice:** `cb7381c0` — REKERS extraction attempt (failure points: probe-first vs list-first, pacing control, 16 uncertainties surfaced)
- **Evidence conversations (7 analyzed via parallel agents):**
  - `87720057` — REKERS failure case (correction loops, no rhythm, 60%+ session on corrections)
  - `451ac158` — AVO creation pass (blank → parts decomposed)
  - `39bd95d4` — Abtmayr-Reichert extraction → meeting agenda (14 uncertainties, one-by-one)
  - `1443f1aa` — Archibus extraction → design doc (10 uncertainties, per-element sources — cleanest)
  - `ed3ed7d2` — AVO reverse extraction (questions first, then transcripts — adaptive variant)
  - `00e6a51f` — Soloforce implicit success (Socratic dialogue as extraction, transcript mining pattern)
  - `b9e0c145` — AVO additional evidence
  - `67641e8a` — AVO UPDATE in practice (diff-based rhythm, dual validation, 3 review rounds on Success Definition)
- **UPDATE extraction pass session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/3bb274e9-cbb9-4bc3-b474-23e2f85b505f.jsonl
- **ASSESS extraction pass session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/f880f009-fe13-4c26-8bac-b2c2cd6a1773.jsonl
- **Design docs demonstrating extraction output:** [Chain 1B Step 3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design) (per-element source attribution, Defined/Partially Defined/Undefined per part)

**Shared references:**
- **Meeting agendas analyzed:** [Mujahid - Asset Code](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/mujahid-meeting-agenda-asset-code-hosting), [Miguel/Ian - Hierarchy](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/miguel-ian-meeting-agenda-hierarchy-understanding), [Rein - Background Data](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-background-data), [Rein - Step 3 API](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/ryan-meeting-agenda-step3-insertion-api), [All-Hands Prioritization](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/all-hands-ai-work-track-prioritization), [Bulk Entry Spike](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/bulk-entry-spike-agenda)
- **CCI:** [#395](https://github.com/DaveX2001/claude-code-improvements/issues/395) — Design doc workflow, [#468](https://github.com/DaveX2001/claude-code-improvements/issues/468) — Extraction pass methodology
- **Design docs analyzed:** [Asset Code Generator](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/asset-code-generator-design), [Chain 1B Bulk Entry](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design), [Chain 1B Step 3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design), [Step 2 Schema Mapping](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-2-schema-mapping-design), [YouTube Marketing Funnel](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/youtube-marketing-funnel-execution-design)
- **Framework:** Michael Gerber, *The E-Myth Revisited*, Chapters 10, 15; [MG-0080 Operations Manual Guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-operations-manual-guide)
