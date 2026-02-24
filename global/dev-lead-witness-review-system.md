---
publish: true
---

# Dev Lead Witness & Review System
[[business-identity]]

System design for how the Dev Lead conducts witness testing, review, and quality feedback in multi-operator delivery.

---

## Problem Statement

Ship with Confidence defines three quality layers (sanity check, AC verification, human witness) but does not map who owns each layer, what depth each role performs, or what the output of each layer looks like. Position Agreements reference "human witness" as a deliverable without defining methodology, tooling, or attention requirements.

When a solo operator holds all positions, this works — tacit knowledge about what to test, where the authoritative rubric lives, and whether the codebase is coherent stays unified in one person. When positions are held by different people, that tacit knowledge does not transfer. The result: the Dev Lead absorbs 3-4 hours of deep witness work that should have been the Developer's responsibility, creates ad-hoc tooling during review instead of before, and falls into the technician trap — doing fixes instead of generating feedback.

**Preconditions:**
- Multi-operator delivery (Developer and Dev Lead are different people)
- Ship with Confidence testing pyramid exists and is stable
- Position Agreements for Developer and Dev Lead exist
- At least one project-level test rubric exists (client-delivered or JA-defined)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Dev Lead reviews to generate feedback — never to do fixes. Every quality layer has an explicit owner, methodology, and output format. |
| **Success** | Dev Lead spot-checks using developer-prepared materials (witness report + rubric cards + spot-check recommendations). Developer deep-witnesses before adding `review` label. |
| **Done test** | Can the Dev Lead complete a spot-check witness using only what the Developer prepared? If the Dev Lead is building scripts, investigating infrastructure, or fixing code — the system is failing. |

---

## Approach

### Part 1: Quality Layer Ownership

Ship with Confidence defines three quality layers. Each layer has a different owner, depth, and output format.

| Layer | Owner | Depth | Output |
|-------|-------|-------|--------|
| **Sanity Check** | Developer | Does it run? Does it not crash? | Pass/fail in session |
| **AC Verification** | Developer (AI) | Does the implementation match the spec? Objective, automatable. | `verification.jsonl` — machine-readable trace |
| **Human Witness (Deep)** | Developer | Does the output actually look right? Sequential attention, one verification item at a time (question, scenario, sub-issue — depends on project). | Issue comment: witness report + spot-check recommendations |
| **Human Witness (Spot-check)** | Dev Lead | Quick verification using developer-prepared materials. Gut-check: "does this feel right?" | Issue comment: approval or feedback items routed back to developer |

**Key distinction:** ACs test issue-level spec compliance (objective). Human witness tests subjective quality that ACs structurally cannot capture — e.g., incorrect references, hallucinated content, format issues that "look off." These are different layers, not different depths of the same thing.

**Attention mode:** Human witness requires single-window focus. Implementation and spec work can be multi-terminal. The Dev Lead cannot review while doing other work — same constraint as spec-design review.

### Part 2: Test Infrastructure Ownership

Test infrastructure splits across three positions. Each position owns a different aspect. Escalation flows upward: Developer → Dev Lead → JA.

| Position | Owns | Example (IITR #789 — illustrative, not exhaustive) |
|----------|------|---------------|
| **JA** | **Defines** — rubric in design doc: what to test, success metrics, expected outcomes, fidelity requirements ("test tool must mirror production pipeline") | 29 test questions, accuracy targets, source references ✅ |
| **Dev Lead** | **Provisions** — authoritative version deployed before developer starts. Deduplicates competing versions. Verifies accessibility. Periodic fidelity check. | 4 competing rubric versions, no single source designated ❌ |
| **Developer** | **Executes** — builds test harness, runs tests, performs deep witness against authoritative rubric. Signals issues upward. | Built harness, ran against wrong rubric version ⚠️ |

**Two distinct things:** The test *rubric* (data — questions + expected answers) and the test *infrastructure* (code — harness, debug scripts, faithful pipeline mirror). Both follow the same three-way split. The JA defines what to test and what "faithful" means. The Dev Lead provisions the authoritative data and verifies tool fidelity. The Developer builds and runs the tools.

**Single source of truth:** For every project with a test rubric, there is exactly ONE authoritative version. The Dev Lead designates it before delegation. All other copies (e.g., server files, Drive exports, hippocampus snapshots) derive from it. When the authoritative version changes, the Dev Lead is responsible for propagating the update.

**Escalation chain:** When something is wrong with the rubric or tooling:
- **Developer** finds rubric/tool issue during execution → signals **Dev Lead** via issue comment
- **Dev Lead** finds design gap during provisioning or fidelity check → signals **JA** via issue comment
- **JA** updates design doc → cycle restarts

### Part 3: Feedback-Not-Fixes

The Dev Lead reviews to generate feedback. Never to do fixes.

When the Dev Lead spot-checks and finds an issue, the output is an issue comment describing what was found — not a code change, not a hotfix, not a script built ad-hoc. That comment routes back to the Developer's workstream. The Developer fixes. The Dev Lead re-checks.

**The E-Myth trap:** The moment the Dev Lead starts fixing, they have fallen back into the technician role. The solo operator's instinct is "faster to fix it myself." In multi-operator delivery, this instinct destroys the system — the Developer never learns, the Dev Lead burns hours on work that isn't their accountability, and the feedback loop that makes each iteration more effective never forms.

**The cycle:**
1. Developer does deep witness → posts witness report as issue comment
2. Dev Lead spot-checks using developer-prepared materials → finds issues
3. Dev Lead posts findings as issue comment (feedback, not fixes)
4. Developer receives feedback → fixes → re-runs witness → updates comment
5. Dev Lead re-checks → approves or generates more feedback

**When the Dev Lead IS the solo operator:** This principle still applies conceptually. When wearing the Dev Lead hat, generate feedback (observations, issue comments). When wearing the Developer hat, do the fixes. The hat-switch is the discipline — even for one person, separating review from execution prevents the "quick fix" that introduces new debt.

### Part 4: Communication Standards

Two principles ensure the Dev Lead has visibility without needing conversation log access.

**Witness Reports:** After deep witness, the Developer communicates findings and spot-check recommendations via issue comment. This is the handoff artifact — without it, the Dev Lead rediscovers what to test from scratch.

**Deviation Signals:** When the Developer goes off-plan (adds data, changes approach, creates unspecified infrastructure), they signal the deviation. Going off-plan is fine — initiative is valued. Invisible deviation is the failure. The communication medium and exact format are TBD — the principle is: the Dev Lead learns about deviations before the review, not during it.

### Part 5: Codebase Coherence Audit

Before delegating an epic to a Developer, the Dev Lead verifies the codebase is in a consistent state. The Developer implements what ACs say — they cannot be held responsible for architectural debt they inherit.

**Principle:** The Dev Lead is the bridge between the design spec and the codebase. If the codebase is messy, undocumented, or internally contradictory, that is the Dev Lead's problem to surface — either cleaning it up or flagging it as a precondition before the Developer starts.

**Examples (not limited to):** Dead config nothing reads, conflicting mechanisms where only one is active, repo vs deployment drift.

**Trigger:** Per epic, before first sub-issue assignment. Especially when a project transitions from solo-operated to multi-operated code.

**Undefined:** Exact checklist, frequency, and depth — routed to [#807](https://github.com/DaveX2001/deliverable-tracking/issues/807).

---

## Source

- **Post-mortem:** [#789 IITR DS-Kit Navigation](https://github.com/DaveX2001/deliverable-tracking/issues/789) — first multi-operator delivery, 3-4 hour witness pain
- **Observations:** [#605 Dev Lead Position Epic](https://github.com/DaveX2001/claude-code-improvements/issues/605) — 17 observations clustered into this system
- **Position Agreements:** [Dev Lead PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/dev-lead-position-agreement-wilsch-ai-services) | [Developer PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-position-agreement-wilsch-ai-services)
- **Methodology:** [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/344de871-d0b0-47b8-a44c-a01f726b24fc.jsonl
