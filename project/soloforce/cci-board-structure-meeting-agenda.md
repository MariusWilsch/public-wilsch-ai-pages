---
publish: true
---

# CCI Board Structure — Open Design Questions
[[wilsch-ai-internal]]

## Meeting Goal

Resolve two open design questions from the CCI Board Structure design doc so the model can move from design to implementation:

1. **Origin resolution** — how the capture skill determines observation origin (Part 2)
2. **Deployment pipeline** — the full position pipeline from observation to production-deployed fix (Part 6)

## Pre-Read

- [CCI Board Structure Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/cci-board-structure-design)
- [System Engineer PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/system-engineer-position-agreement-wilsch-ai-services) — Standards 2-4, Strategic Work #2
- [/improve-system Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The observation origin field has no auto-resolution mechanism
⏱️ 10 min

The observation format includes an origin field (traceline/beta-user, internal/team, internal/marius) that gives the System Engineer a prioritization signal. The design doc states "auto-resolved from session context" but the resolution mechanism is undefined.

- GitHub username (github.actor) could distinguish Marius from team from beta users
- Project context (soloforce = internal, plugin repo = traceline) could distinguish scope
- Workflow dispatch context in the Traceline plugin could carry origin metadata
- Explicit user input is the simplest but highest-friction option

**To resolve:** The mechanism by which the capture skill determines observation origin without user input.

### 2. The improvement cycle follows a position pipeline with undefined stage gates
⏱️ 20 min

The extraction pass surfaced a pipeline: observations accumulate → SE monitors pressure → release epic → JA creates design doc → Developer implements → stability gate (R&D → team → Traceline). This connects the CCI board to the improve-system architecture and the E-Myth position pipeline, but the stage transitions are implicit.

- The SE's "pressure threshold" is human judgment — no formal metric exists for when a cluster warrants a release epic
- The JA design doc for a release epic may differ from a project design doc — observations as sources vs. transcripts as sources
- Stability gates between deployment stages (R&D → team → Traceline) are currently Marius's judgment without formalization
- The improve-system architecture (Session A → B → C) predates the position pipeline — integration points need definition

**To resolve:** The stage gates and position handoffs in the deployment pipeline from observation to production fix.

## Meeting Format

- **Type:** Working session (self-facilitated)
- **Expectation:** Review design doc Part 6 + improve-system architecture
- **Outcome:** Pipeline stages formalized as design doc update (Undefined → Defined)

## Related

- **Issue:** [DaveX2001/deliverable-tracking#819](https://github.com/DaveX2001/deliverable-tracking/issues/819)
- **Design Doc:** [CCI Board Structure](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/cci-board-structure-design)
