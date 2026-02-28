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
| **AC Verification** | Developer (AI) | Is the system state correct? Objective, automatable. | `verification.jsonl` — machine-readable trace |
| **Human Witness (Deep)** | Developer | Does the experience work? AI-guided teaching session, one step at a time. | Witness section in `tracking.md` — steps, expected, witnessed |
| **Human Witness (Spot-check)** | Dev Lead | Quick validation using developer's witness trace. | Issue comment: approval or feedback items routed back to developer |

**Key distinction — Verification vs Validation:** AC verification catches *specified* failures (system state checks via automation). Human witness catches *unspecified* failures (experience quality via human judgment). These are different layers, not different depths of the same thing. See [AC-DoD Framework § 1](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework) for the three-gate model.

**Witness session mechanism (`/witness` skill):** After implementation, the AI derives a guided tour from three sources: ACs in tracking.md, implementation context (URLs, credentials, surfaces), and project-level test rubrics (from the epic body, data artifacts, or issue comments). The AI curates — it selects the most revealing test cases, explains why it picked them, and links to the full rubric for additional coverage.

The tour is structured as a natural experience flow, not per-AC tests. Each tour step annotates which ACs it covers, providing traceability without forcing the human to experience the feature as a checklist. Infrastructure ACs (document counts, config values, file existence) stay machine-only in verification.jsonl — the witness tour covers only experiential ACs where human judgment adds value.

No design-time witness hints are needed. The ACs themselves serve as the source — the AI derives concrete steps post-implementation because URLs, paths, and credentials only stabilize after the feature is built.

The AI presents steps one-by-one to the human. The human executes each step, reports what they see, and the AI records findings in the tracking.md witness section. The developer runs the witness session for deep coverage. The Dev Lead follows the same trace for spot-check (pick 2-3 steps based on judgment, verify on staging).

**The teaching test (Feynman principle):** The AI that built the feature must prove understanding by teaching the human to experience it. The witness session is not a test plan — it is a teaching demonstration. If the AI's instructions don't match what the human sees, either the implementation or the AI's understanding has gaps. This separation also future-proofs to AI-to-AI validation: swap the human executor for a second AI in a fresh context (no implementation bias).

**Attention mode:** Human witness requires single-window focus. Implementation and spec work can be multi-terminal. The Dev Lead cannot review while doing other work — same constraint as spec-design review.

**Spot-check methodology:** The Dev Lead follows the developer's witness trace in tracking.md. Pick 2-3 steps based on the Dev Lead's own judgment — no curated recommendations needed from the developer. Execute on staging, form judgment. The witness trace IS the prepared material — no separate artifact needed. If the trace is missing or incomplete, that itself is the signal: the developer didn't witness their work.

**Tour examples (feature-type determines shape):**

*UI feature (#757 — Foundation + CMS + Deploy):*
1. Open `http://localhost:3000` → page rendered by Vike (AC1)
2. Open `http://VPS_IP:{port}/admin` → Payload CMS login screen (AC2)
3. Log in → 4 collections in sidebar: projects, memberships, content, legal (AC4)
4. Create a project entry → save → visible in list
5. Open frontend → project rendered from CMS data (AC5)

*RAG pipeline (#797 — Masterfragen Expansion):*
1. Open OpenWebUI on staging → chat interface ready
2. Ask: "Welche englischsprachigen Zertifikate bieten Sie an?" → answer with certificate info (AC2)
3. Ask: "Was kostet ein Datenschutz-Audit?" → answer with €790 + DS-Kit Plus (AC3)

The UI feature produces a 5-step tour through pages and data flow. The RAG pipeline produces a 3-step tour through questions and answers. Infrastructure ACs (doc counts, CSV updates) have no tour step — they stay in verification.jsonl.

### Part 2: Test Infrastructure Ownership

Test infrastructure splits across three positions. Each position owns a different aspect. Escalation flows upward: Developer → Dev Lead → JA.

| Position | Owns | Example (IITR #789 — illustrative, not exhaustive) |
|----------|------|---------------|
| **JA** | **Defines** — rubric in design doc: what to test, success metrics, expected outcomes, fidelity requirements ("test tool must mirror production pipeline") | 29 test questions, accuracy targets, source references ✅ |
| **Dev Lead** | **Provisions** — authoritative version deployed before developer starts. Deduplicates competing versions. Verifies accessibility. Periodic fidelity check. | 4 competing rubric versions, no single source designated ❌ |
| **Developer** | **Executes** — builds test harness, runs tests, performs deep witness against authoritative rubric. Signals issues upward. | Built harness, ran against wrong rubric version ⚠️ |

**Issue-level vs project-level:** The Developer creates ACs for each issue (how to verify THIS implementation). The JA designs the project-level test rubric (how to verify the PROJECT works as a whole). The Developer should not be expected to figure out the test rubric design — that is a spec-design artifact.

**Two distinct things:** The test *rubric* (data — questions + expected answers) and the test *infrastructure* (code — harness, debug scripts, faithful pipeline mirror). Both follow the same three-way split. The JA defines what to test and what "faithful" means. The Dev Lead provisions the authoritative data and verifies tool fidelity. The Developer builds and runs the tools.

**Single source of truth:** For every project with a test rubric, there is exactly ONE authoritative version. The Dev Lead designates it before delegation. All other copies (e.g., server files, Drive exports, hippocampus snapshots) derive from it. When the authoritative version changes, the Dev Lead is responsible for propagating the update.

**Escalation chain:** When something is wrong with the rubric or tooling:
- **Developer** finds rubric/tool issue during execution → signals **Dev Lead** via issue comment
- **Dev Lead** finds design gap during provisioning or fidelity check → signals **JA** via issue comment
- **JA** updates design doc → cycle restarts

**Undefined:** Operational provisioning steps — what "designate and deploy the authoritative version" looks like as a concrete procedure per project type.

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

**Undefined:** Structure and routing mechanism for feedback comments — what a feedback comment contains, how it gets assigned back to the Developer's workstream.

### Part 4: Communication Standards

Two principles ensure the Dev Lead has visibility without needing conversation log access.

**Witness Reports:** After deep witness, the Developer communicates findings and spot-check recommendations via issue comment. This is the handoff artifact — without it, the Dev Lead rediscovers what to test from scratch.

**Deviation Signals:** When the Developer goes off-plan (adds data, changes approach, creates unspecified infrastructure), they signal the deviation. Going off-plan is fine — initiative is valued. Invisible deviation is the failure. The communication medium and exact format are TBD — the principle is: the Dev Lead learns about deviations before the review, not during it.

**Undefined:** Exact format and structure for witness reports and deviation signals — to be defined through practice as more multi-operator deliveries are executed.

### Part 5: Codebase Coherence Audit

Before delegating an epic to a Developer, the Dev Lead verifies the codebase is in a consistent state.

**Developer boundary:** The Developer operates at issue-level — ACs, implementation within design doc scope, issue-level test harness. The Developer is NOT responsible for project-level concerns: codebase coherence, test rubric design, cross-issue architectural consistency, or inherited technical debt. Those are Dev Lead and JA concerns respectively.

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
- **Session (initial):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/344de871-d0b0-47b8-a44c-a01f726b24fc.jsonl
- **Session (Feynman Test extraction):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/4b8d4a93-6ccb-4b4f-ac6a-4b8500619905.jsonl
- **Session (Ceremony + witness tour extraction):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/83d4d2fe-f380-41ee-90e1-82ad154feca5.jsonl
