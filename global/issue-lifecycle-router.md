---
publish: true
date: 2026-01-22
source: Rubber-duck session on issue #362
---

# Issue Lifecycle Router

[[team-scaling]]

Router document for issue lifecycle. Issues follow one of two paths based on type assigned at creation.

## Type Distinction

| Type | Description | Context Requirement | Executor |
|------|-------------|---------------------|----------|
| **spec-design** | Creates specs to be executed later | Needs context OUTSIDE the issue | Marius |
| **spec-implement** | Implementable directly | Everything needed is IN the issue | David, Mohamed |

**Key insight:** Type is about context containment, not output format.

---

## Path Overview

### Path A: spec-design (3 stages)

```
Stage 1: Creation
  └─ Shell + spec-design label
       ↓
Stage 2: Design = Execution
  └─ rubber-duck creates artifact
       ↓
Stage 3: Closing
  └─ Artifact determines action:
     • Internal spec → Create spec-implement issue
     • Client deliverable → Send to client
  └─ Label "done", issue closed
```

### Path B: spec-implement (5 stages)

```
Stage 1: Creation
  └─ Shell + spec-implement label
       ↓
Stage 2: Design
  └─ rubber-duck + ac-create (DoD + AC)
       ↓
Stage 3: Execution
  └─ Delegate to team, Ship with Confidence workflow
       ↓
Stage 4: Review
  └─ Marius batch review, human witness (business)
       ↓
Stage 5: Closing
  └─ Production deployed, issue closed
```

---

## Stage 1: CREATION

### What Happens

Work is identified as needing tracking. The `deliverable-tracking` skill creates a shell and assigns type label.

### Tool: deliverable-tracking

| Creates | Format |
|---------|--------|
| **What?** | Deliverable description |
| **Why?** | Motivation/importance |
| **Tracking** | `[DoD + AC](to be created via /rubber-duck)` (placeholder) |
| **Notes** | Optional references, blockers, context |
| **Label** | `backlog` + (`spec-design` OR `spec-implement`) |

### Type Determination

| Type | Assign When |
|------|-------------|
| `spec-design` | Needs context outside the issue (exploration, decisions required) |
| `spec-implement` | Self-contained (everything needed is in the issue) |

**If wrong:** Corrected during Design stage (rubber-duck reveals truth).

### Purpose

**Reminder with type clarity** - shell exists, type known, content TBD.

### Constraint

**Does not conflate implementation and design elements.**

An issue should be EITHER about designing something OR about implementing something - never both.

### Router Links

| Doc | Purpose |
|-----|---------|
| [deliverable-tracking skill](~/.claude/skills/deliverable-tracking/SKILL.md) | Creates shell + type label |
| [Spec-Design/Spec-Implement ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/adr-adopt-spec-creation-execution-label-distinction) | Type distinction and verification differences |

### Gap Analysis

| Gap | Where | What's Needed |
|-----|-------|---------------|
| **Type assignment** | deliverable-tracking skill | Ask type at creation, apply label |
| **Mix prevention** | deliverable-tracking skill | Detect conflated issues → prompt split |
| **design-doc skill** | NEW skill | Standalone skill for design docs |
| **Protocol: Lifecycle start** | CLAUDE.md | AI understands issues start as reminders, get refined (abstract concept) |
| **Label rename** | ADR + GitHub | spec-creation → spec-design, spec-execution → spec-implement |

---

## Stage 2: DESIGN

### What Happens

Pull issue from backlog. Process differs by type.

### Process by Type

#### spec-design path

```
1. Pull issue (onboarding)
2. rubber-duck = Think + Create spec simultaneously
   - Output: design doc, implementation issue, or other artifact
3. → Closing (Stage 3)
```

**No separate execution stage** - rubber-duck session IS the execution for spec-design.

#### spec-implement path

```
1. Pull issue (onboarding)
2. Externalize thinking (rubber-duck)
3. Define DoD + AC (ac-create)
     ↓
=== DECISION POINT (after ac-create) ===
     ↓
4. → Delegate to team (Stage 3: Execution)
```

### Tools

| Tool | Used For |
|------|----------|
| **rubber-duck** | Both types: externalization |
| **ac-create** | spec-implement only: DoD + AC |
| **design-doc** (NEW) | spec-design: create design doc artifact |

### Purpose

- **spec-design:** Think through problem + create specification artifact (same session)
- **spec-implement:** Prepare issue for delegation (DoD + AC defined)

### Router Links

| Doc | URL | Purpose |
|-----|-----|---------|
| **AC DoD Framework** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework) | DoD vs AC distinction, enumeration method |
| **task-lifecycle-reference** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/task-lifecycle-reference) | Rubber-duck mode definition, phase overview |
| rubber-duck skill | Local | Externalization methodology |
| ac-create skill | Local | DoD + AC (spec-implement) |
| design-doc skill (NEW) | TBD | Create design artifacts |

### Gap Analysis

| Gap | Where | What's Needed |
|-----|-------|---------------|
| **Type-aware workflow** | Skills | Clear branching: spec-design → rubber-duck only, spec-implement → rubber-duck + ac-create |
| **design-doc skill** | NEW skill | Standalone skill with template |
| **design-doc trigger** | TBD | Behavioral trigger - WHEN during rubber-duck to invoke |
| **Type correction path** | TBD (either rubber-duck OR ac-create) | Update label if type was wrong at creation |
| **Work type branching** | Skills | Different workflows for design vs implementation work |

---

## Stage 3: EXECUTION

### Path A: spec-design

**Stage 2 = Execution** (combined)

```
Design (rubber-duck) creates artifact directly:
  - Design doc in hippocampus
  - Follow-up implementation issue
  - Other specification artifact
     ↓
→ Closing (Stage 3 for this path)
```

**No separate Stage 3** - execution happens during Design.

### Path B: spec-implement

**Stage 3: Separate Execution**

```
After Design: DoD + AC defined, delegated to team
     ↓
New session (David/Mohamed):
  1. Create worktree for issue
  2. Implement against DoD + AC
  3. AC verification (AI)
  4. Deploy to staging
  5. Smoke test
  6. Human witness (technical)
  7. Add `review` label
     ↓
→ Review (Stage 4)
```

### Tools (spec-implement only)

| Tool | Purpose |
|------|---------|
| worktree skill | Create isolated worktree |
| ac-verify skill | Run Given-When-Then verification |

### Router Links

| Doc | URL | Purpose |
|-----|-----|---------|
| **Ship with Confidence** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) | Full execution → testing → review workflow |

### Gap Analysis

| Gap | Where | What's Needed |
|-----|-------|---------------|
| None | - | Ship with Confidence covers spec-implement execution |

---

## Stage 4: REVIEW

### Path A: spec-design

**No Review stage** - Marius creates and owns the artifact, no handoff.

```
After Design (artifact created):
     ↓
→ Closing (directly)
```

### Path B: spec-implement

**Stage 4: Review**

```
After Execution: `review` label added by team
     ↓
Marius batch review session:
  1. Pull issues with `review` label
  2. Human witness (business perspective)
     - "Is this what I wanted?"
     - Freeform discovery
  3. If issues found → comment → back to Execution
  4. If pass → approve production merge
     ↓
→ Closing (Stage 5)
```

### Tools (spec-implement only)

| Tool | Purpose |
|------|---------|
| ac-verify | Optional re-verification by Marius |
| Human witness | Freeform discovery, business perspective |

### Router Links

| Doc | URL | Purpose |
|-----|-----|---------|
| **Ship with Confidence** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) | Human Witness methodology, Review workflow |

### Gap Analysis

| Gap | Where | What's Needed |
|-----|-------|---------------|
| None | - | Ship with Confidence covers review |

---

## Stage 5: CLOSING

### Path A: spec-design

**Stage 3: Closing**

```
After Design (artifact created):
     ↓
Artifact type determines action:
  - Internal spec → Create spec-implement issue (deliverable-tracking)
  - Client deliverable → Send to client
     ↓
Change issue label to "done"
Issue closed
```

**Outputs:**
- New spec-implement issue (if internal)
- Sent artifact (if client deliverable)

### Path B: spec-implement

**Stage 5: Closing**

```
After Review (Marius approves):
     ↓
1. Merge PR to production
2. Deploy to production server
3. Issue closed
```

### Router Links

| Doc | URL | Purpose |
|-----|-----|---------|
| **Ship with Confidence** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) | Rollback Strategy (if issues arise post-deploy) |

### Gap Analysis

| Gap | Where | What's Needed |
|-----|-------|---------------|
| **Protocol: Lifecycle end** | CLAUDE.md | AI understands tasks need closing behavior (abstract concept) |
| **Closing workflow** | Skills / board SOP | Specific implementation of closing (labels, status changes) |

---

## Architecture: Protocol vs Skill Separation

**Critical principle:** Protocol is tool-agnostic. Skills handle specific implementations.

| Layer | Contains | Portable? | Example |
|-------|----------|-----------|---------|
| **Protocol** | Abstract concepts | Yes (works with GitHub, Asana, Linear, etc.) | "Issues start as reminders", "Tasks need closing behavior" |
| **Skill/Command** | Specific implementations | No (tied to current tools) | `spec-design`/`spec-implement` labels, `deliverable-tracking`, GitHub workflows |

**What belongs where:**

| Concept | Layer | Why |
|---------|-------|-----|
| "Issues start as reminders, get refined" | Protocol | Abstract lifecycle start |
| "Tasks need closing behavior" | Protocol | Abstract lifecycle end |
| Design vs implementation work types | Skill | Our specific workflow distinction |
| `spec-design` / `spec-implement` labels | Skill | GitHub-specific implementation |
| `deliverable-tracking` workflow | Skill | GitHub-specific tool |
| "done" label application | Skill | GitHub-specific |

**Implication for gaps:** Protocol gaps should be ABSTRACT concepts the AI needs to understand. Skill gaps are SPECIFIC implementations to build.

---

## Meta-Level References

These documents apply across all stages:

| Doc | URL | Purpose |
|-----|-----|---------|
| **Invocation Taxonomy** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/claude-code-extension-invocation-taxonomy) | Protocol vs Commands vs Skills - when to use what |
| **Skills as Pre-packaged HOWs** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/skill-lifecycle-execution-timing) | How skills work in lifecycle |

---

## Full Gap Analysis Summary

### Protocol Gaps (Abstract Concepts)

| Gap | What AI Needs to Understand |
|-----|----------------------------|
| **Lifecycle start** | Issues start as reminders, get refined over time |
| **Lifecycle end** | Tasks need closing behavior when complete |

### Skill/Command Gaps (Specific Implementations)

| Gap | Stage | Where | What's Needed |
|-----|-------|-------|---------------|
| Type assignment | 1 | deliverable-tracking | Ask type at creation |
| Mix prevention | 1 | deliverable-tracking | Validate single-phase |
| Label rename | 1 | ADR + GitHub | spec-creation → spec-design, spec-execution → spec-implement |
| Type-aware workflow | 2 | Skills | Branch by type (spec-design vs spec-implement) |
| design-doc skill | 2 | NEW | Standalone skill + template |
| design-doc trigger | 2 | TBD | Behavioral trigger |
| Type correction | 2 | TBD | Update label if wrong |
| Closing workflow | 5 | Skills / Board SOP | Specific closing implementation |

---

## Related Documents

- [AC DoD Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework)
- [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence)
- [task-lifecycle-reference](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/task-lifecycle-reference)
- [Spec-Creation/Spec-Execution ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/adr-adopt-spec-creation-execution-label-distinction) (to be updated with new labels)
- [Skills as Pre-packaged HOWs](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/skill-lifecycle-execution-timing)
