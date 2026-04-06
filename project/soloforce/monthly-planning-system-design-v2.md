---
publish: true
---

# Monthly Planning System — Design Doc (v2)
[[coo-position]]

Redesign of the monthly planning methodology. Replaces the Package Model (v1) with a three-lens evaluation framework after March practice invalidated the project-position coupling. Supersedes [v1](monthly-planning-system-design).

---

## Problem Statement

The March Monthly Planning System solved instinct-driven daily decisions by introducing packages (project + position = unit). Five extraction passes produced a coherent methodology: packages narrowed the space, the Shutdown Ritual enforced it, milestones drove urgency.

March practice invalidated the package coupling. Position work advanced independently of project contexts — driven by organizational bottlenecks, not project friction. Multiple positions were exercised within single projects. CCI position epics required week-scale human judgment, not session-scale package selection. The package prerequisite ("a position needs a project to justify its timeslot") became the constraint the system outgrew.

The monthly plan's core principle survives: pre-decide direction so daily decisions become evaluations, not deliberations. What changes is the organizing unit and the evaluation lens.

**Preconditions:**
- Package Model (v1) documented across 5 extraction passes — stable reference
- March daily log (23 entries) + shutdown ritual commits (15) provide empirical evidence of the shift
- Position work demonstrated independent urgency (CCI #600, #602 — organizational bottleneck)
- Design Partner model emerging (#847) — changes what "client" means
- March revenue €18,736 vs €8K target — cash flow no longer the primary constraint

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | The monthly plan evaluates each client through three lenses (cash flow, validation, IP) and each position commitment independently — producing a context window that makes daily decisions trivial evaluations |
| **Success** | The per-client walkthrough produces a weighted scoring that replaces the docket. Position work appears on the plan as a first-class citizen alongside project work. The Shutdown Ritual selects from both streams without requiring package coupling. |
| **Done test** | The April 2026 plan artifact validates the methodology — produced using v2, reviewed at month-end. If the plan held through April without reverting to instinct-driven daily decisions → design is complete. |

---

## Approach

### Part 1: The Three-Lens Framework

The package model assumed project and position form a single unit — "working on Archibus automatically means improving the Developer position." Practice invalidated this: position work advanced independently, multiple positions were exercised within single projects, and urgency drove daily selection, not package affinity.

**Three lenses replace the package as the organizing principle.** Each client engagement is evaluated through:

| Lens | What it measures | Example |
|------|-----------------|--------|
| **Cash Flow** | Retainer stability — trading hourly rate for predictable monthly income. Success fee as uplift. | Archibus: €3,720/mo retainer through Aug |
| **Validation** | Does this client prove the AI-native model works? Position system as AI workforce, Design Partner as engagement pattern, observation system as improvement loop. | Archibus: DP origin. IITR: low — classic agency. |
| **IP** | What stays proprietary? Three-slice ownership: Agent + Improvement Loop always Wilsch AI. Methodology that travels client to client. | Positions, agents compound across engagements |

**Position work is a first-class citizen.** No longer a byproduct of project delivery — it has its own terminals, its own forcing function (organizational bottleneck visibility), and dedicated maker time. CCI board dissolves; position epics move to the Commitments Board alongside client epics. Both axes are independent commitments, governed by the same milestone mechanics.

**The monthly plan is a context window.** Each daily decision conditioned on the monthly direction — "where we want to be at end of month." The Shutdown Ritual, sprint board visibility, and milestone-driven forcing functions (social + financial consequences of client meetings) turn the plan into a live evaluation filter. Instinct prioritizes within this forced field — it is flavor, not the engine.

**The docket may dissolve.** Without packages, the per-client three-lens walkthrough replaces package formation. The monthly plan evaluates each engagement on cash flow, validation, and IP contribution (weighted), and each position commitment independently.

**Undefined:** Design Partner model — validation lens's proof point and company direction. → [#847](https://github.com/DaveX2001/deliverable-tracking/issues/847) (handoff posted)

**Undefined:** Per-client scoring schema — the weighted evaluation replacing the docket. → Next extraction pass.

---

## Source

- **v1 Design Doc:** [Monthly Planning System Design](monthly-planning-system-design)
- **v1 Monthly Plan:** [Feb 23 – Mar 31, 2026](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/monthly-plan-2026-02-23-to-2026-03-31)
- **Issue:** [#884 Monthly Capacity Plan](https://github.com/DaveX2001/deliverable-tracking/issues/884)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/402c5530-e86a-489c-987f-204d1f1bb764.jsonl` (extraction pass 1 — root question: what replaces the package model)
- **Fireflies:** [Kate meeting (2026-04-05)](https://app.fireflies.ai/view/01KNEQ3KWRD2YPAS490WVP8WYT) — cash flow / validation / IP framing, Design Partner as test bed, three contract scenarios
- **Data Artifacts:**
  - [Stakes Visibility: Forcing Function](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/stakes-visibility-forcing-function)
  - [CCI Board Structure Design](monthly-planning-system-design) (local — `~/.claude/hippocampus/project/soloforce/cci-board-structure-design.md`)
  - [Contract Strategy Design](contract-strategy-retainer-model-design) (local — unpublished)
  - [#847 Contract Strategy](https://github.com/DaveX2001/deliverable-tracking/issues/847) — Design Partner handoff posted
  - [#1308 Jeremy Design Partner](https://github.com/DaveX2001/deliverable-tracking/issues/1308) — handoff posted
  - [#1336 Lead System Agent](https://github.com/DaveX2001/deliverable-tracking/issues/1336) — lead handling handoff

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

