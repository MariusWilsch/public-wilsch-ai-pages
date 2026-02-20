---
publish: true
---

# CCI Board Structure — Open Design Questions
[[wilsch-ai-internal]]

## Meeting Goal

Resolve three open design questions from the CCI Board Structure design doc so the model can move from design to implementation:

1. **Label taxonomy mapping** — how position × scope labels translate to position epics
2. **Observation format standard** — what a well-written observation looks like
3. **Transfer mechanism** — how observations move from position epic to release epic

## Pre-Read

- [CCI Board Structure Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/cci-board-structure-design)
- [System Engineer PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/system-engineer-position-agreement-wilsch-ai-services) — Standards 2-4, Strategic Work #2

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The label taxonomy has two dimensions — position and scope
⏱️ 10 min

The triage sessions created labels with a position × scope pattern: `traceline/developer`, `internal/system-engineer`, etc. Position epics need a 1:1 mapping to these labels, but the scope dimension (traceline vs internal) creates ambiguity.

- Five label combinations exist: traceline/developer, traceline/system-engineer, internal/developer, internal/system-engineer, internal/ja
- One position epic per label = 5 epics (fine-grained)
- One position epic per position = 3 epics (developer, system-engineer, ja) with scope as metadata
- Traceline is a product; internal is infrastructure — different priorities

**To resolve:** The mapping between the label taxonomy and position epic creation.

### 2. The observation format needs a "how to write" standard
⏱️ 15 min

The design doc defines the two-part structure (context as hook, source as pond) and the principle that hooks must be problem-based, never solution-based. What's missing is the guide that gets embodied into the capture tooling so observations are consistently useful.

- Current flag-for-improvement asks free-form questions — no structural enforcement
- A bad observation: "The AI should use better formatting" (solution-based, no source)
- A good observation: "AI skipped the SURFACE list format during probe session" + conversation path (problem-based, sourced)
- The guide needs to be short enough to embed in a skill prompt

**To resolve:** The observation format standard that flag-for-improvement enforces at capture time.

### 3. Moving observations from position epic to release epic
⏱️ 10 min

When a release epic is created, observations transfer from the position epic. The transfer mechanism affects whether the position epic stays clean and whether evidence is preserved in the release context.

- Copy content to release epic comment → delete from position epic (clean but destructive)
- Link to original comment from release epic → mark original with reaction (non-destructive but noisy)
- Move by re-posting with attribution → delete original (clean and traceable)
- GitHub has no native "move comment" feature

**To resolve:** The transfer mechanism that keeps position epics clean while preserving evidence in release epics.

## Meeting Format

- **Type:** Working session (self-facilitated)
- **Expectation:** Review the design doc pre-read, bring examples of good/bad observations from recent sessions
- **Outcome:** Three decisions documented as design doc updates (Undefined → Defined)

## Related

- **Issue:** [DaveX2001/deliverable-tracking#819](https://github.com/DaveX2001/deliverable-tracking/issues/819)
- **Design Doc:** [CCI Board Structure](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/cci-board-structure-design)
