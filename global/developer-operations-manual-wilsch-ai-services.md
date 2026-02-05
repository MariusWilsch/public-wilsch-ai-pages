---
publish: true
---

# Developer Operations Manual - Wilsch AI Services

[[life-vision]]

Operations Manual for the Developer position at Wilsch AI Services. Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 15 (Management Strategy) + E-Myth Operations Manual Guide (MG-0080).

**Position:** Developer

**Reports To:** Dev Lead

**Version:** v1 (Draft - 2026-02-05)

---

## How to Use This Manual

This is your "owner's manual" for the Developer position. It tells you:
- **How your day flows** (Morning Start → Work → End of Day)
- **What tools to use** (skills guide you through the methodology)
- **Why things work this way** (logic behind each process)

**Read the referenced skills once** to understand WHY they exist. The skills guide you through the HOW via prompts.

---

## Section 1-3: Company-Wide (TBD)

*To be created: Company Story, Products & Services, Policies*

For now, reference:
- [Strategic Objective](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
- [Organization Chart](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
- [Primary Aim](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/primary-aim-life-vision)

---

## Section 4: Position

[Developer Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-position-agreement-wilsch-ai-services)

---

## Section 5: Systems (Developer Workflow)

### Morning Start

**Your "package in the mailbox":** GitHub Projects → Deliverable Tracking → Mohamed view

1. **Open your view:** [Deliverable Tracking Board](https://github.com/orgs/DaveX2001/projects/1/views/7)
   - Filter: `assignee:mo-adel007 -status:Backlog,Done`
   - You see: To-Do, In Progress, Review

2. **Pick work:**
   - IF In Progress has items → **Continue and finish** (get things done)
   - ELSE pick from **To-Do by priority** (priority set in grooming)
   - IF nothing available → Flag to Marius

3. **Check issue type:**
   - `maker/spec-design` → Path A
   - `maker/spec-implement` → Path B

**Why this order:** Finishing In Progress work prevents context switching waste. Priority comes from grooming discussions.

---

### Path A: Spec-Design Issue

**Purpose:** Transform an unclear requirement into a clear, implementable spec.

**Closing criteria:** DoD + AC created (or documented failure with learning).

**Flow:**

```
1. /onboarding → choose spec-design issue
2. /rubber-duck → externalize thinking, resolve ambiguities
   └─ Goal: Shared understanding between you and AI
   └─ Output: Confidence ✓ → proceed to ac-create
            OR Keep thinking → stay in rubber-duck
3. IF Confidence: /ac-create → define DoD + AC
   └─ Skill guides you through 7-step pipeline
   └─ 4 Refinement Gate checks (user judgment on each)
4. Output: NEW spec-implement issue (via deliverable-tracking)
5. Close spec-design issue
```

**Key skills to read (understand WHY):**
- `/rubber-duck` - Externalization methodology
- `/ac-create` - DoD/AC framework, Given-When-Then format

**Your responsibility:** Think out loud. Answer when AI asks. Flag when you can't reach shared understanding.

**AI responsibility:** Guide through methodology, resolve technical ambiguities via research, present options for your judgment.

---

### Path B: Spec-Implement Issue

**Purpose:** Build what was specified in DoD + AC.

**Closing criteria:** All DoD checkboxes complete, deployed to staging, review label added.

**Flow:**

```
1. /onboarding → choose spec-implement issue
2. (optional) /rubber-duck → if you want to think through implementation
3. Clarity Workflow (system guides automatically):
   ├─ Requirements-Clarity → resolve WHAT ambiguities
   ├─ Implementation-Clarity → resolve HOW ambiguities
   │   └─ Worktree skill discovered here
   └─ Evaluation-Clarity → resolve VERIFICATION approach (sanity checks)
4. Execute (one pass at a time from DoD)
5. Commit → issue-commit-linker auto-creates comment
6. Sanity check (page renders? API responds? DB exists?)
7. Human witness (technical) → click through, verify it works
8. Add `review` label
```

**Key concept - Clarity Workflow:** AI resolves its own ambiguities before executing:
- User preference? → AskUserQuestion
- Technical ambiguity? → Online research via sub-agent
- Codebase question? → Search via grep/glob

**Your responsibility:** Respond to AskUserQuestion prompts. Approve/deny skill invocations. Do human witness.

**AI responsibility:** Run clarity phases, discover needed skills, execute against DoD + AC, commit with descriptive messages.

**Reference:** [Three-Session Model](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model)

**TBD (needs deep-dive):**
- Clarity Workflow detail (req-clarity → impl-clarity → eval-clarity)
- Execute phase mechanics (one pass at a time)
- Commit phase / task complete protocol

---

### End of Day

**Purpose:** Leave a clean handoff so Marius can review asynchronously.

**Ritual:**

1. **For completed work:**
   - Add `review` label to issue
   - Run `/issue-comment` with status summary
   - Marius reviews next morning

2. **For In Progress work:**
   - Commit + push current state
   - No special action needed (stays In Progress)

**Why this matters:** Marius can start reviewing your completed work without waiting for a sync.

*(TBD: Review section needed — spec-design reviewed before spec-implement starts, spec-implement reviewed before merge to main)*

---

### Blocked/Stuck (TBD)

*What to do when you're stuck or blocked during work.*

**To be defined:** Escalation path, try-before-asking threshold, documentation requirements.

**Current guideline:** From Position Agreement - "Try at least 3 different approaches (with AI assistance) before escalating design issues to Solution Architect."

---

## Gaps to Fill (Future Sessions)

- [ ] Review section (spec-design review, spec-implement review)
- [ ] Worktree workflow detail
- [ ] Deploy to staging procedure *(partial: see Ship with Confidence)*
- [ ] Smoke test checklist *(partial: see Ship with Confidence, project-specific)*
- [ ] **Staging environment setup** (prerequisite per project — containers, configs, so Mohamed can deploy without Marius)
- [ ] Blocked/Stuck escalation protocol
- [ ] Priority visibility in GitHub
- [ ] Company sections (1-3): Story, Products & Services, Policies
- [ ] Grooming from Mohamed's perspective

---

## Reference Documents

| Document | Purpose |
|----------|---------|
| [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) | Master routing - which path, which stage |
| [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) | Testing pyramid - AC → Smoke → Human Witness |
| [AC DoD Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework) | Acceptance criteria methodology |
| [Three-Session Model](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model) | Design / Implementation / Verification separation |
| [GROOMING.md](https://github.com/DaveX2001/deliverable-tracking/blob/main/docs/GROOMING.md) | Daily sync process |

---

## Source

- E-Myth Management Strategy interview (2026-02-05)
- Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 15
- Template: E-Myth Operations Manual Guide (MG-0080)
