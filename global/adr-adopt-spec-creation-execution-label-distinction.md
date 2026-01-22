---
publish: true
---
# ADR: Adopt Spec-Creation / Spec-Execution Label Distinction

[[team-scaling]]

## Status

Accepted (2026-01-22)

## Context

After 6-8 weeks working with the Paul YouTube Faceless client, a clear pattern emerged: **I cannot do everything alone anymore.**

The project ended with the client relationship terminating. Key learning: by the time I tried to delegate (14 days past deadline), there was no time to specify work for handoff. Issues existed with bodies (What/Why/Architecture) but tracking files were empty - no ACs, no clear "done" criteria for someone else to verify against.

**Analysis of real issues revealed:**

| Issue Type | Example | Characteristic |
|------------|---------|----------------|
| Paul #491 (tracking.md) | Empty placeholder | No ACs - not delegation-ready |
| Internal #320 (tracking.md) | Full ACs, Given-When-Then | Clear verification - delegation-ready |

The distinction isn't about whether issues have structure - both had issue bodies. It's about **what the issue outputs**:

- Some issues output **specifications** (workflow definitions, architecture decisions, alignment documents)
- Some issues output **implementations** (code, features, configurations from a specification)

The verification method differs fundamentally between these types. Spec-creation outputs artifacts (documents, diagrams) - verified by DoD ("does this exist and cover the requirements?"). Spec-execution outputs behavior - verified by ACs ("does the code behave as specified?"). This distinction determines what's delegable: ACs enable someone else to verify without your judgment.

**The existing maker/manager labels don't capture this:**
- Maker = focused deep work (could be either type)
- Manager = quick coordination (admin, emails)
- Both spec-creation and spec-execution are maker work

## Decision

**Adopt two new labels that distinguish issues by their output:**

### Labels

| Label | Meaning | Who |
|-------|---------|-----|
| `spec-creation` | Output is a specification (workflow, decision, design doc) | Marius |
| `spec-execution` | Output is implementation of an existing specification | David, Mohamed |

### Hierarchy

```
MAKER (focused work)
├── spec-creation  ← Designing specifications (Marius)
└── spec-execution ← Implementing from specifications (David, Mohamed)

MANAGER (quick coordination)
└── unchanged (admin, emails, decisions)
```

### Team Mapping

| Person | Scope |
|--------|-------|
| **Marius** | Spec-creation (all complexity levels) |
| **David** | Spec-execution: single-phase systems (agents, bots, admin/ops, content) |
| **Mohamed** | Spec-execution: multi-phase systems (backend + database + frontend) |

### Handoff Criteria

**Build → Learn → Refine approach:** Don't over-define upfront. Start using labels based on judgment, observe patterns, formalize criteria over time.

Initial heuristic: If the issue's Definition of Done includes "Define X" or "Design X" as the primary deliverable, it's likely spec-creation. If it includes "Implement X" or "Build X" per an existing design, it's spec-execution.

### Verification by Type

The verification method differs by issue type:

| Issue Type | DoD | ACs (Given-When-Then) | Why |
|------------|-----|----------------------|-----|
| **spec-creation** | Required | Optional/rare | Output is an artifact (document, diagram), not behavior |
| **spec-execution** | Required | Required | ACs enable delegation - someone else can verify "does code match AC?" |

**Rationale:** ACs are designed for behavioral verification ("Given X, When Y, Then Z"). Spec-creation outputs artifacts, not behavior. You can't meaningfully write: "Given problem context, When stakeholder reads spec, Then they understand" - that's subjective.

**For spec-creation, DoD handles verification:**
- "Workflow diagram exists"
- "Decision covers options A, B, C"
- "Stakeholders aligned"
- "Reviewed by X"

**For spec-execution, ACs enable delegation:**
- Clear pass/fail criteria
- Someone else can verify without needing your judgment
- This is what makes handoff possible

## Research Basis

| Term | Source |
|------|--------|
| Discovery vs Delivery | Marty Cagan (Inspired) |
| Problem Space vs Solution Space | Product management discipline |
| Work ON vs Work IN the business | Michael Gerber (E-Myth Revisited) |

The E-Myth describes this as the core founder struggle: entrepreneurs start doing technical work, realize they need to work ON systems, but can't let go of execution because no one else "gets it."

**The backpressure insight** (from banay.me): Systems with automated feedback mechanisms (builds, type checkers, tests) enable spec-execution delegation. Without backpressure, the context holder spends their time manually catching errors.

## Consequences

**Positive:**
- Clear visual distinction on board: "Marius needs to design this" vs "ready for delegation"
- Enables working ON the business (spec-creation) vs IN the business (spec-execution)
- Scalable: as team grows, spec-execution capacity grows
- Forces specification discipline: issues must be spec-complete before delegation

**Negative:**
- Additional labels to maintain
- Handoff point requires judgment (not fully automated)
- Spec-creation still bottlenecked on Marius

**Mitigations:**
- Learn handoff criteria through real examples
- Build backpressure systems (tests, types, builds) to enable spec-execution quality
- Eventually: can spec-creation itself be partially delegated?

## Implementation

1. **Create labels** in deliverable-tracking:
   ```bash
   gh label create 'spec-creation' --description 'Output is a specification' --repo DaveX2001/deliverable-tracking
   gh label create 'spec-execution' --description 'Implementing from a specification' --repo DaveX2001/deliverable-tracking
   ```

2. **Update BOARD-SOP.md:** Add labels to documentation, update grooming questions

3. **Apply to existing issues:** During grooming, classify backlog/to-do items

4. **Observe patterns:** Document what makes handoff work vs fail

## Related

- [Maker/Manager Schedule ADR](./adr-adopt-maker-manager-schedule-separation.md) - Work style separation
- [BOARD-SOP.md](https://github.com/DaveX2001/deliverable-tracking/blob/main/docs/BOARD-SOP.md) - Board documentation
- [Don't Waste Your Backpressure](https://banay.me/dont-waste-your-backpressure/) - Feedback mechanisms for delegation
