---
publish: true
---

# Monthly Planning System — Design Doc
[[coo-position]]

Design doc for a repeatable monthly planning methodology that pre-decides company direction so daily decisions become trivial evaluations — not fresh deliberations. Associated with the COO/President position.

---

## Problem Statement

Day-by-day decision-making produces smart instincts, not strategy. Running three positions simultaneously without a planning framework means daily choices are driven by urgency, not by which work advances the company toward its vision of operating as a shareholder outside the business.

The calendar solved this for time — fixed blocks pre-decide when meetings happen, when maker work happens, when to stop. No equivalent exists for the company itself: which projects, which positions, which contracts, which leads — all decided daily by instinct.

**Preconditions:**
- Multiple active client projects (5+) competing for maker time
- Position-building work (PAs, OMs, methodology) happening alongside project delivery
- Retainer/contract model not yet standardized (#847)
- E-Myth organizational system partially built — org chart, position agreements, and operations manuals at various stages of completion
- Calendar time-boxing already operational (ON/IN the business split)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Decisions are pre-made. When new work arrives, the response is a simple yes/no evaluation against the monthly plan — not a fresh deliberation. Ambiguity in daily decision-making is eliminated. |
| **Success** | Monthly planning session produces a project-position package map. The Shutdown Ritual enforces it nightly ("which 3 issues serve the plan?"). New leads, scope changes, and meetings evaluate against the plan like calendar time-boxes evaluate meeting requests: "does it fit?" |
| **Done test** | Can I write a meeting agenda with open design questions about the monthly planning system? If NO → design is complete |

---

## Approach

### Part 1: Three Organizational Axes

The company operates on three axes. Each has its own tracking system. The monthly plan is the bridge.

| System | Axis | Tracks |
|--------|------|--------|
| **Deliverable-tracking** | Project (GitHub labels) | Client work, delivery |
| **CCI board** | Position (position epics) | AI behavior improvements |
| **Monthly plan** | Package (project + position) | How projects drive position improvement |

Deliverable-tracking answers "what are we delivering?" CCI answers "what are we improving?" The monthly plan answers "why this project improves this position" — the bridge between doing work and building the company.

### Part 2: The Package Model

A project and its associated position are a single unit — the package. Working on Archibus automatically means improving the Developer position. Working on UWI means exercising the full SA → Developer → Dev Lead chain. There is no separate decision about "which position to focus on."

Packages change month by month. This month's Archibus-Developer package may become next month's AVO-Developer package. The position stays, the project rotates. This gives a full month of scope — no pressure to rush because the package holds for 30 days.

The monthly plan lists packages, not projects or positions independently. Deadlines from contracts and retainers drive which package gets attention. Position improvement is a byproduct of project delivery, not a standalone activity — but it is the key value being gained. The position methodology is the scalable product that gets sold. Project delivery funds the research; the research produces the product.

**Outcome-based reporting:** Each package produces two reportable outcomes: (1) what was delivered on the project, and (2) what improved on the position. Associating conversations with issues enables performance review of AI operator sessions — tracing position improvement back to specific project delivery.

One package per maker session. Multi-terminal setup within the session:
- System Engineer terminal (improves AI behavior just-in-time)
- JA terminal (designs)
- Developer terminal (implements)
- Session C (blind verification) available immediately
- One backup terminal for ad-hoc work

**Undefined:** Multi-terminal persona distribution model — how terminals coordinate within a single maker session. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

### Part 3: The Monthly Planning Session

**Cadence:** Last 2-3 days of the current month. Enter the new month knowing what's up — same principle as entering maker time at the top of the hour. Session requires 2-3 hours focused (context loading overhead for reading scope documents at 2.5x speed).

**The walkthrough IS the methodology.** The planning session walks through each active project and asks:

1. **Project inventory:** What projects are active? (GitHub labels)
2. **Friction analysis:** What's the friction in each project?
3. **Position mapping:** Which position improvement would make each project faster — and benefit all other projects?
4. **Package formation:** Project + position = package for the month
5. **Precondition check:** Do I have enough projects to observe friction patterns? If not → take on leads.

This was validated live in the first extraction pass — the walkthrough produced the project-position map that became Part 2.

**What the plan does NOT decide:** Which position to focus on separately. The projects are given (contracts, retainers). The position follows from the project. The plan maps the relationship and makes it visible.

**Stable vs unstable:** Most things that feel unstable are actually undecided. The planning session turns undecided → stable through pre-decisions. Examples:
- "Taking on new leads this month?" → Yes/No (pre-decided)
- "Does this meeting serve the plan?" → Yes/No (evaluates against plan)
- "Which issues tomorrow?" → Shutdown Ritual selects from plan's packages

Only external events (client loss, legal disputes, team exits) are truly unstable — handled by buffer capacity, not re-planning.

### Part 4: Enforcement — Shutdown Ritual Connection

The monthly plan is strategy. The Shutdown Ritual is the enforcement mechanism — the operational trigger that makes the plan real.

**Reframe:** The nightly ritual question changes from "which 3 issues tomorrow?" to "which 3 issues serve this month's plan?"

**Accountability loop:**
1. Monthly plan sets the target (packages for the month)
2. Shutdown Ritual shows yesterday's progress AND selects tomorrow's work
3. Selection evaluates against the monthly plan
4. If no issues serve the plan → create new ones, or the plan is wrong

Without this connection, the monthly plan is a document. With it, every evening forces a check against the plan.

The Shutdown Ritual's integration with the monthly plan is the plan page itself — the docket table provides the package filter, and project deadlines (carried instinctively, not on the plan) provide the urgency signal for which package gets tomorrow's maker blocks. The ritual question shifts from "which 3 issues?" to "which 3 issues from my active packages?" The meaning connection — seeing the "why" per package nightly — reinforces the link between daily work and the company vision. Mechanical details (progress view, procedural changes) will be evaluated empirically after the first month's run.

### Part 5: Position Progress & the Router

Each position progresses through a document pipeline visible on the [Operations Manual Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/operations-manual-router):

PA Draft → PA Complete → OM TOC Draft → OM TOC Complete → OM Sections → Handoff

Handoff = 100%. The cycle is complete, though the position continues evolving post-handoff. The Router already tracks Draft / TBD / Complete per document per position.

The monthly plan uses stages, not percentages: "Developer position: OM complete, handoff in progress" is more useful than "75%." Monthly milestones move positions from one stage to the next — through project work, not through abstract documentation.

Currently, the Developer position is the only one with a full OM (v3) — built over a year of active use. Other positions are earlier in the pipeline (PA drafts, TBD). This asymmetry is expected: positions advance through project work, and the Developer position had the most project hours.

### Part 6: Walkthrough Example (February 2026)

The following project-position map was produced during the first walkthrough. It changes every month — this is an example of the methodology's output, not a permanent state.

| Project | Primary Position | Friction |
|---------|-----------------|----------|
| Archibus | Developer (autonomous path) | Orchestration, role-switching |
| IITR | Developer → Dev Lead | Decomposition success case |
| UWI | Full chain (JA→Dev→Dev Lead) | Handoff speed between roles |
| AVO | JA + Developer (autonomous) | Ralph experiment, local dev |
| Rekers | JA + Developer (autonomous) | Same as AVO, waiting on Thomas |
| Traceline | System Engineer + Developer | Beta friction → improvement loop |

**Cross-cutting friction surfaced through the walkthrough:**
- JA → Developer decomposition (methodology gap)
- Developer → Dev Lead review (tooling gap)
- Non-interactive Developer path (untested)

This is what Part 3's walkthrough produces. Each month's output differs.

**Undefined:** Team capacity — who can fill which positions, current skill levels, availability. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

**Undefined:** Contract model (#847) — retainer structure that pre-decides projects. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

### Part 7: The Monthly Plan Artifact

The walkthrough (Part 3) produces the analysis. The monthly plan artifact records the decisions. It is a stateless decision record — not a progress tracker. Nothing on this page changes within the month. When something new arrives, you point at the plan and evaluate: does it fit?

**Location:** The plan lives on the [Operations Manual Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/operations-manual-router) as a "Monthly Plans" section — one row per month, each linking to a published page. Current month shows Active; past months show Archived. The Router is already the daily bookmark; the plan becomes part of that daily surface.

**Document structure:**

The plan page contains two sections:

**1. The Docket** — a table of active packages for the month:

| Package (Client → Position) | Owner | Why | Board |
|-----------------------------|-------|-----|-------|
| {Client} → {Position} | {Name} | {Friction or strategic reason from walkthrough} | [→ view]({filtered board URL}) |

Each row answers: what client-position combination, who owns it, why it was selected this month (the reasoning from the walkthrough's friction analysis), and a direct link to the filtered GitHub board view for that project's issues.

"Why" is per package — the reasoning that makes this client-position combination active. It does not change mid-month. It is the decision record from the walkthrough.

**2. Pre-Decisions** — binary choices made once at month start, eliminating mid-month deliberation:

- **New leads:** Yes/No (with constraints if Yes)
- Additional pre-decisions develop empirically — start with leads, add categories as recurring deliberations emerge from practice

**The stateless principle:** Deadlines, milestones, and progress are deliberately excluded. Deadlines live in the operator's head and in contracts — adding them to the plan creates maintenance burden that turns the page into stale shelf-ware. The plan narrows the decision space to 3-4 active packages. Within that small space, instinct combined with known deadlines is sufficient for daily prioritization. The calendar doesn't rank which time block is "most important" — it shows them all. The plan works the same way.

**Three-layer evaluation for new work:**

1. **Vision filter** (permanent): Does this advance the AI-native agency operating model?
2. **Package filter** (monthly): Does this fit one of the docket's active packages?
3. **Calendar filter** (daily): Is there a maker block available?

All three must pass. This is the equivalent of the calendar's time-box evaluation — "does it fit?" — applied to work instead of time.

**Not on the docket = not this month.** No separate "parked" section. Packages either appear on the docket or they don't. If they return, they appear on next month's docket.

**Undefined:** Month-end evaluation methodology — how to assess whether the month's packages achieved their intended friction reduction. Deferred until after the first month's empirical run (March 2026). → [Meeting Agenda](monthly-planning-system-meeting-agenda)

**Undefined:** Walkthrough-to-docket selection framework — the decision mechanism for how the walkthrough's full package landscape gets cut down to the active docket. Currently a judgment call; may need criteria after the first formal walkthrough. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

---

## Source

- **Design Docs:**
  - [Operations Manual Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/operations-manual-router)
  - [Developer Position Beta Scope](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope)
  - [Primary Aim - Life Vision](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/primary-aim-life-vision)
  - [Organization Chart](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
  - [Strategic Objective](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
  - [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router)
  - [System Engineer PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/system-engineer-position-agreement-wilsch-ai-services)
  - [CCI Board Structure Design](~/.claude/hippocampus/project/soloforce/cci-board-structure-design.md)
- **Issues:**
  - [#884 March capacity plan](https://github.com/DaveX2001/deliverable-tracking/issues/884)
  - [#847 Contract Strategy](https://github.com/DaveX2001/deliverable-tracking/issues/847)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/87151f23-d09a-4ed4-a4fe-71368c49c52d.jsonl`
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/af0f8b80-edc4-4aaa-8b99-b107cf4b48a9.jsonl` (extraction pass 2 — plan artifact format, stateless principle, pre-decisions, evaluation stack)
