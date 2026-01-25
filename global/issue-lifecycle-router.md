---
publish: true
---

# Issue Lifecycle Router

[[team-scaling]]

Router document for issue lifecycle. Issues follow one of two paths based on type assigned at creation.

---

## Foundational Principles (Protocol-Level)

### Issues Are Cheap

**"We cannot rely on anybody to be aware of anything. The system must be aware of everything."**

| Principle | Meaning |
|-----------|---------|
| **Immediate capture** | Work only exists in issues - email comes in → create issue immediately |
| **Fast closing** | Issues should close quickly, not linger in backlog |
| **Cascade creation** | Close one → spawn next if needed (maker/spec-design → maker/spec-implement) |
| **No floating context** | Nothing lives outside the system - no emails sitting, no "I'll remember" |

### Closure Clarity

**Every issue has clear "when is this done?"**

| Type | Closure Criteria |
|------|------------------|
| **maker/spec-design** | Artifact created (design doc, follow-up issue) |
| **maker/spec-implement** | DoD + AC verified |
| **Waiting for feedback** | Set deadline - close if no response by X |

**AI should infer closure criteria** based on type. This is automatic, not a separate decision.

---

## Type Distinction

### Maker Work (Technical)

| Type | Description | Context Requirement | Executor |
|------|-------------|---------------------|----------|
| **maker/spec-design** | Creates specs to be executed later | Needs context OUTSIDE the issue | Marius |
| **maker/spec-implement** | Implementable directly | Everything needed is IN the issue | David, Mohamed |

### Manager Work (Admin/Coordination)

| Type | Description | Context Requirement | Executor |
|------|-------------|---------------------|----------|
| **manager** | Admin, coordination, waiting | Client-facing OR needs judgment | Marius |
| **manager** (Zineb-eligible) | Clicking, collecting, follow-ups | Non-client-facing, self-contained | Zineb |

**Key insight:** Type is about context containment, not output format.

### Team Role Definitions

**Detailed role definitions live in separate documents:**

| Role | Document |
|------|----------|
| **Zineb** (Virtual Assistant) | [Zineb Role Definition](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/zineb-role-definition) |
| **David/Mohamed** (Implementers) | *TBD - role definition to be created* |

**Quick reference:**
- **Zineb:** Doing (clicking, collecting) vs Deciding (drafting, judging) - see full doc
- **David/Mohamed:** Execute maker/spec-implement issues against DoD + AC

---

## Path Overview

### Path A: maker/spec-design (3 stages)

```
Stage 1: Creation
  └─ Shell + maker/spec-design label
       ↓
Stage 2: Design = Execution
  └─ rubber-duck creates artifact
       ↓
Stage 3: Closing
  └─ Artifact determines action:
     • Internal spec → Create maker/spec-implement issue
     • Client deliverable → Send to client
  └─ Label "done", issue closed
```

### Path B: maker/spec-implement (5 stages)

```
Stage 1: Creation
  └─ Shell + maker/spec-implement label
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

### Path C: manager (2 stages)

```
Stage 1: Assignment
  └─ Issue assigned to executor (Marius or Zineb)
       ↓
Stage 2: Execution + Closing
  └─ Task completed, issue closed
```

**Zineb-eligible manager work:**
```
Stage 1: Assignment
  └─ Zineb assigned in daily sync
       ↓
Stage 2: Prep
  └─ Zineb clicks, collects, organizes
       ↓
Stage 3: Approval
  └─ Marius reviews prep, approves execution
       ↓
Stage 4: Execute + Close
  └─ Zineb executes, comments on issue, issue closed
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
| **Label** | `backlog` + (`maker/spec-design` OR `maker/spec-implement`) |

### Type Determination

| Type | Assign When |
|------|-------------|
| `maker/spec-design` | Needs context outside the issue (exploration, decisions required) |
| `maker/spec-implement` | Self-contained (everything needed is in the issue) |

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

---

## Stage 2: DESIGN

### What Happens

Pull issue from backlog. Process differs by type.

### Process by Type

#### maker/spec-design path

```
1. Pull issue (onboarding)
2. rubber-duck = Think + Create spec simultaneously
   - Output: design doc, implementation issue, or other artifact
3. → Closing (Stage 3)
```

**No separate execution stage** - rubber-duck session IS the execution for maker/spec-design.

#### maker/spec-implement path

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
| **ac-create** | maker/spec-implement only: DoD + AC |
| **design-doc** (NEW) | maker/spec-design: create design doc artifact |

### Purpose

- **maker/spec-design:** Think through problem + create specification artifact (same session)
- **maker/spec-implement:** Prepare issue for delegation (DoD + AC defined)

### Router Links

| Doc | URL | Purpose |
|-----|-----|---------|
| **AC DoD Framework** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework) | DoD vs AC distinction, enumeration method |
| **task-lifecycle-reference** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/task-lifecycle-reference) | Rubber-duck mode definition, phase overview |
| rubber-duck skill | Local | Externalization methodology |
| ac-create skill | Local | DoD + AC (maker/spec-implement) |
| design-doc skill (NEW) | TBD | Create design artifacts |

---

## Stage 3: EXECUTION

### Path A: maker/spec-design

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

### Path B: maker/spec-implement

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

### Tools (maker/spec-implement only)

| Tool | Purpose |
|------|---------|
| worktree skill | Create isolated worktree |
| ac-verify skill | Run Given-When-Then verification |

### Router Links

| Doc | URL | Purpose |
|-----|-----|---------|
| **Ship with Confidence** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) | Full execution → testing → review workflow |

---

## Stage 4: REVIEW

### Path A: maker/spec-design

**No Review stage** - Marius creates and owns the artifact, no handoff.

```
After Design (artifact created):
     ↓
→ Closing (directly)
```

### Path B: maker/spec-implement

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

### Tools (maker/spec-implement only)

| Tool | Purpose |
|------|---------|
| ac-verify | Optional re-verification by Marius |
| Human witness | Freeform discovery, business perspective |

### Router Links

| Doc | URL | Purpose |
|-----|-----|---------|
| **Ship with Confidence** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) | Human Witness methodology, Review workflow |

---

## Stage 5: CLOSING

### Path A: maker/spec-design

**Stage 3: Closing**

```
After Design (artifact created):
     ↓
Artifact type determines action:
  - Internal spec → Create maker/spec-implement issue (deliverable-tracking)
  - Client deliverable → Send to client
     ↓
Change issue label to "done"
Issue closed
```

**Outputs:**
- New maker/spec-implement issue (if internal)
- Sent artifact (if client deliverable)

### Path B: maker/spec-implement

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

---

## Architecture: Protocol vs Skill Separation

**Critical principle:** Protocol is tool-agnostic. Skills handle specific implementations.

| Layer | Contains | Portable? | Example |
|-------|----------|-----------|---------|
| **Protocol** | Abstract concepts | Yes (works with GitHub, Asana, Linear, etc.) | "Issues start as reminders", "Tasks need closing behavior" |
| **Skill/Command** | Specific implementations | No (tied to current tools) | `maker/spec-design`/`maker/spec-implement` labels, `deliverable-tracking`, GitHub workflows |

**What belongs where:**

| Concept | Layer | Why |
|---------|-------|-----|
| "Issues start as reminders, get refined" | Protocol | Abstract lifecycle start |
| "Tasks need closing behavior" | Protocol | Abstract lifecycle end |
| Design vs implementation work types | Skill | Our specific workflow distinction |
| `maker/spec-design` / `maker/spec-implement` labels | Skill | GitHub-specific implementation |
| `deliverable-tracking` workflow | Skill | GitHub-specific tool |
| "done" label application | Skill | GitHub-specific |

**Implication for gaps:** Protocol gaps should be ABSTRACT concepts the AI needs to understand. Skill gaps are SPECIFIC implementations to build.

---

## Meta-Level References

These documents apply across all stages:

| Doc | URL | Purpose |
|-----|-----|---------|
| **Maker/Manager Schedule ADR** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/adr-adopt-maker-manager-schedule-separation) | Work style separation (ON vs IN the business) |
| **Board SOP** | [Link](https://github.com/DaveX2001/deliverable-tracking/blob/main/docs/BOARD-SOP.md) | Board documentation and label usage |
| **Invocation Taxonomy** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/claude-code-extension-invocation-taxonomy) | Protocol vs Commands vs Skills - when to use what |
| **Skills as Pre-packaged HOWs** | [Link](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/skill-lifecycle-execution-timing) | How skills work in lifecycle |

---

## Related Documents

- [AC DoD Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework)
- [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence)
- [task-lifecycle-reference](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/task-lifecycle-reference)
- [Spec-Creation/Spec-Execution ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/adr-adopt-spec-creation-execution-label-distinction) (to be updated with new labels)
- [Skills as Pre-packaged HOWs](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/skill-lifecycle-execution-timing)
