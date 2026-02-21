---
publish: true
---

# Monthly Planning System — Meeting Agenda
[[coo-position]]

## Meeting Goal

Resolve the undefined elements of the Monthly Planning System that require either deeper probing or empirical evidence from the first March instantiation.

1. **Team & capacity model** — who fills which positions, session budgets, availability
2. **Contract model** — retainer structure that pre-decides projects
3. **Operational integration** — Shutdown Ritual evolution, multi-terminal model, success criteria

## Pre-Read

- [Monthly Planning System Design Doc](monthly-planning-system-design)
- [Operations Manual Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/operations-manual-router) — position progress dashboard
- [Contract Strategy #847](https://github.com/DaveX2001/deliverable-tracking/issues/847) — retainer model context

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Team capacity model
⏱️ 20 min

The package model requires knowing who can fill which positions. David completed decomposition on IITR. Ralph is a potential for spec-design. Thomas handles infrastructure. Christoph builds the Traceline tutorial.

- Current team: David, Ralph, Thomas, Christoph, Ulrich
- Each person's position capability and availability is implicit, not documented
- The monthly plan needs named capacity: "David can handle 2 Developer packages"

**To resolve:** Team roster with per-person position capability and monthly availability, documented as an input to the planning walkthrough.

### 2. Contract model as project pre-decider
⏱️ 15 min

Retainers eliminate the "which projects" decision — contracts pre-decide everything downstream. Hourly billing is breaking because multi-project work makes single-dimension time tracking impossible. The retainer model from #847 is the mechanism that turns "which projects" from a monthly decision into a contractual given.

- Archibus is closest to the desired contract model (extended 6 months)
- Abtmayr-Reichert contract type was a mistake
- Stafford comp model (base + success fee) is being negotiated
- #847 owns this design space — this topic connects monthly planning to the contract work, not duplicates it

**To resolve:** How the contract model from #847 feeds into the monthly planning walkthrough as a pre-decided input.

### 3. Multi-terminal persona distribution
⏱️ 15 min

The package model runs one package per maker session with multiple Claude Code terminals: SE + JA + Developer + backup. The just-in-time benefit is that SE improvements feed directly into the same session's developer work — Session C verification becomes immediately available.

- How terminals coordinate within a single maker session
- Whether SE observations from one terminal can influence the Developer terminal in real-time
- Practical constraints: context windows, session isolation, attention splitting

**To resolve:** Operational model for multi-terminal maker sessions — how the human orchestrates 3-4 concurrent Claude Code personas.

### 4. Shutdown Ritual evolution
⏱️ 10 min

The Shutdown Ritual currently selects 3 maker issues for tomorrow's physical book without evaluating against a monthly plan. The monthly planning system requires the ritual to ask "which 3 issues serve THIS month's plan?" and show yesterday's progress alongside tomorrow's selection.

- Current ritual: data quality check + maker selection (max 3)
- Missing: monthly plan filter, progress view (past + future)
- The ritual is the forcing function — without it, the plan is just a document

**To resolve:** Ritual redesign that integrates monthly plan evaluation into the nightly selection workflow.

### 5. Monthly success criteria
⏱️ 10 min

The monthly plan needs a "done" definition. Without it, month-end assessment is subjective. The walkthrough produces packages with associated position stages — but how to measure whether the month succeeded or not is undefined.

- Possible: position stage advancement ("Dev Lead went from TBD to PA Draft")
- Possible: project milestone ("IITR deployed to production")
- Possible: combined package outcome

**To resolve:** What a successful month looks like, measured against the package model.

### 6. Strategic Objective update
⏱️ 10 min

The SO (written Feb 3-4) describes a dev shop with a proprietary method. Current thinking has evolved: positions are products, the org structure itself is the vision product, the company is an AI research lab for organizational strategy. The three-product hierarchy (client deliverables → position methods → org system for acquisitions) is confirmed but not yet written into the SO.

- SO references: "What we sell: finished AI projects"
- Evolved: "What we sell: position methodologies. What we build: org system for boomer businesses."
- "Every employee I have is an AI use case" — the thesis in one sentence

**To resolve:** SO update scope — which sections change and how the three-product hierarchy integrates.

### 7. Position transition methodology gaps
⏱️ 15 min

The cross-cutting friction from the walkthrough: JA → Developer decomposition is a methodology gap, and Developer → Dev Lead review is a tooling gap. David completed IITR decomposition successfully — why he doesn't struggle while Marius does is an open question.

- JA → Developer: no documented process for turning design docs into developer issues
- Developer → Dev Lead: Review Queue designed in the ILR but not built
- David's success: potential evidence that the methodology works for non-experts
- Rohdex + evidence chain system: future agent/triage angle (deep undefined)

**To resolve:** Whether these gaps need their own design docs or can be resolved through the next project walkthrough.

## Meeting Format

- **Type:** Extraction pass (JA session)
- **Expectation:** Pre-read the design doc. Come with updated project status and team availability.
- **Outcome:** Resolved undefined markers → design doc updated

## Related

- **Issue:** [#884 March capacity plan](https://github.com/DaveX2001/deliverable-tracking/issues/884)
- **Design Doc:** [Monthly Planning System Design](monthly-planning-system-design)
- **Contract Strategy:** [#847](https://github.com/DaveX2001/deliverable-tracking/issues/847)
