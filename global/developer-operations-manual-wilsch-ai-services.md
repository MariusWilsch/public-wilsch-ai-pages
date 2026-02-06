---
publish: true
---

# Developer Operations Manual - Wilsch AI Services

[[life-vision]]

Operations Manual for the Developer position at Wilsch AI Services. Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 15 (Management Strategy) + E-Myth Operations Manual Guide (MG-0080).

**Position:** Developer

**Reports To:** Dev Lead

**Version:** v2 (Draft - 2026-02-05)

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

**Purpose:** Start each day with clear direction — no guessing what to work on.

---

#### Flow

1. **Open your view:** [Deliverable Tracking Board](https://github.com/orgs/DaveX2001/projects/1/views/7)
   - Filter: `assignee:mo-adel007 -status:Backlog,Done`
   - You see: To-Do, In Progress, Review

2. **Pick work:**
   - IF In Progress has items → **Continue and finish** (get things done)
   - ELSE pick **TOP item from To-Do** (grooming already sorted it)
   - IF nothing in To-Do → Flag to Marius

3. **Check issue type:**
   - `maker/spec-design` → Path A
   - `maker/spec-implement` → Path B

4. **(Optional) Reference yesterday's grooming transcript** if you need context on why this item is priority

---

#### Logic

**Why finish In Progress first:** Context switching is expensive. Completing work > starting new work.

**Why To-Do is already sorted:** Grooming physically reorders items — top = highest priority. You don't need to guess or ask. The system delivers the priority.

**Why transcripts are available:** If you forget what was discussed in grooming, the Fireflies transcript is your reference. You shouldn't need it often, but it's there.

---

### Path A: Spec-Design Issue

**Purpose:** Transform unclear requirements into clear, implementable specification.

**Closing criteria:** DoD + AC created in a new spec-implement issue.

**Why this exists:** You can't build what you don't understand. Spec-design forces thinking BEFORE building — cheap to change a design doc, expensive to change implementation.

---

#### Flow

**Step 1: Pick and Onboard**
```
/onboarding → select spec-design issue
```
AI loads issue context. You see: What, Why, Notes.

---

**Step 2: Externalize Thinking**
```
/rubber-duck
```
- Think out loud — AI asks questions to clarify your understanding
- Goal: Shared understanding between you and AI
- AI signals **Confidence ✓** when ambiguities resolved
- If stuck → ask Marius during office hours

---

**Step 3: Define ACs**
```
/ac-create (when Confidence ✓)
```

**Two concepts you need to understand:**

| Concept | Question | What it means |
|---------|----------|---------------|
| **DoD (Definition of Done)** | "Does it exist?" | What to build — the deliverables |
| **AC (Acceptance Criteria)** | "Does it work?" | How to verify behavior — Given-When-Then tests |

**Key insight:** Parallel gates, not traced. Both must pass independently.

**DoD Example:**
```
- [ ] API endpoint returns user data
- [ ] UI displays user profile
```

**AC Example:**
```
Given: I am logged in
When: I visit /profile
Then: I see my name and email
```

**Your job:** Judge the 4 Refinement Gate checks:
1. Observable Output — "Can I actually SEE this work?"
2. Test Data — "Do we have what we need to test it?"
3. Design Questions — "Are there decisions not documented?"
4. Blocking Dependencies — "What needs to happen before verification?"

The skill guides you through. You judge.

---

**Step 4: Read and Confirm**
- New spec-implement issue is created
- **READ THE ENTIRE ISSUE + ACs**
- 💡 **Tip:** Use Speechify extension to read it aloud — hearing + reading helps catch gaps
- Ask: "Do I understand this? Is this stable?"
- ACs are now LOCKED — changes = new spec-design session

---

**Step 5: Review (Training Phase)**
- Add `review` label to the **spec-design** issue (spec-implement issue already exists from Step 3)
- Marius reviews the ACs on the new spec-implement issue
- If approved → close spec-design issue ✅
- If changes needed → back to Step 2

**Office Hours (Training Phase):** During the 10-25 issue training period, Marius is available on Google Meet (camera off, voice off by default). Unmute/share screen when questions arise. Reference: [Strategic Objective - Training Method](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services#training-method)

---

#### Logic

**Why DoD and AC are separate:**
- DoD tells you what to BUILD
- AC tells you what SUCCESS looks like
- You can build something (DoD ✓) that doesn't work correctly (AC ✗)

**Why Refinement Gate exists:**
Last opportunity to catch problems before implementation. Cheap to fix here, expensive during implementation.

**Why training review exists:**
During 10-25 issues, Marius reviews ACs before implementation to catch misunderstandings early. Later, this moves to grooming. Eventually, trust is established and only finished work is reviewed.

---

### Path B: Spec-Implement Issue

**Purpose:** Build what was specified in DoD + AC.

**This documents Part 1: Implementation Session.** Part 2 (Verify Session) is separate — see [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence).

**Session ends at:** Task Complete (sanity checks pass)

---

#### Flow

**Step 1: Pick and Onboard**
```
/onboarding → select spec-implement issue
```

**What AI loads:**
- Issue body (What, Why, Notes)
- **DoD + ACs from tracking file** — primes AI's understanding
- Last 20 comments

**What you see:** AI suggests a **next step** based on DoD + ACs. Use this to prime your thinking.

**Options during onboarding:**

| Option | When to use |
|--------|-------------|
| **React Frontend README** | Working on React frontend — provides best practices |
| **FastAPI Backend README** | Working on Python backend — provides best practices |
| **Full investigation** | Compare DoDs against codebase — use if you don't remember what's implemented |
| **Skip** | Context is fresh, want to move fast |

**Trade-off:** Latency vs Precision.

---

**Step 2: (Optional) Clarify**
```
/rubber-duck (if needed)
```

**When to use:**
- Picking up after a while — need to reconnect
- Unsure about implementation approach

**When to skip:**
- Moving from spec-design right away — context is fresh
- Path forward is obvious

**Key principle:**
> Don't build on UNCLARIFIED assumptions. If you can't judge whether something is right or wrong, there's no point building it.

---

**Step 3: Clarity Workflow**

**What it is:** The structured opposite of rubber-duck.

| Rubber-duck | Clarity Workflow |
|-------------|------------------|
| Free-form externalized thinking | Takes your thinking → outputs a plan |
| You drive | AI drives, resolves ambiguities |

AI runs three phases:

| Phase | Question |
|-------|----------|
| **Requirements-Clarity** | "Is the WHAT clear?" |
| **Implementation-Clarity** | "Is the HOW clear?" |
| **Evaluation-Clarity** | "How do we VERIFY?" |

**Who AI asks for what:**

| Ambiguity type | Who AI asks |
|----------------|-------------|
| Code structure | Codebase (grep/glob) |
| User preference | You (AskUserQuestion) |
| API docs / external | Internet / MCP |

**Why sub-agent for research:** Avoids polluting context with 78-90k tokens of search results. Returns focused summary.

**Positional arguments:**
```
/requirements-clarity let's do pass one
```
Focuses AI on specific pass. Optional — context from rubber-duck is usually sufficient.

---

**Step 4: Execute**

**Before Execute — ExitPlanMode judgment:**

After Evaluation-Clarity, AI calls ExitPlanMode. Before approving, ask:

> "Do I feel confident this plan will give us an answer to move this issue forward?"

- **Yes** → Approve, let Claude execute
- **No** → Don't approve, clarify first

**During Execute:**
- Execute is autonomous — let Claude work
- Work in **passes** (from DoD dependency analysis)
- Sanity checks happen before Task Complete
- If sanity fails → backpressure → AI tries to fix

**Task Complete:** AI signals when plan is complete and sanity checks pass. Implementation session ends here.

**Valid outputs:**
- ✅ Pass implemented, sanity checks pass
- ✅ "This approach doesn't work" — documented learning is valid
- ❌ Confusion without documentation

---

#### Concepts

**Why Clarity Workflow:** AI resolves its OWN ambiguities before executing. You don't think for AI — AI investigates and asks when stuck.

**Why sanity checks are backpressure:** Task isn't complete unless sanity passes. This gives AI feedback to self-correct.

**Reference:** [Three-Session Model](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model)

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
| [GROOMING.md](https://github.com/DaveX2001/deliverable-tracking/blob/main/GROOMING.md) | Daily sync process |

---

## Source

- E-Myth Management Strategy interview (2026-02-05)
- Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 15
- Template: E-Myth Operations Manual Guide (MG-0080)
