# AC and DoD Framework
[[claude-code-architecture]]

---
publish: true
date: 2026-01-01
source: Rubber-duck session analyzing #377 and #89
---

## 1. Core Concepts

**Definition of Done (DoD)** and **Acceptance Criteria (AC)** are separate concepts:

| Concept | Purpose | Question Answered |
|---------|---------|-------------------|
| **DoD** | What to build | "Does it exist?" |
| **AC** | How to verify it works | "Does it behave correctly?" |

**Key insight:** Parallel gates, not traced to each other. Both must pass independently.

**Source:** [Scrum.org](https://www.scrum.org/resources/blog/what-difference-between-definition-done-and-acceptance-criteria)

---

## 2. AC Scoping Principle

> **AC = User-observable behavior at the boundary where value is delivered**

- Focus on WHAT users observe, not HOW system works internally
- Each feature owns its observable boundary
- Internal effects become observable in downstream features

**Decision tree:**
- Is this describing HOW it's implemented? → Too narrow (implementation detail)
- Is this describing WHAT value users receive? → Correct scope
- Is this describing EVERYTHING the feature does? → Too broad (split the story)

---

## 3. AC Enumeration Method

**Step 1:** List user choices (inputs)
```
Create Video Step 1:
- script_type: [youtube_url, custom_script, title_gen]
- autopilot: [true, false]
```

**Step 2:** Enumerate combinations
```
3 script_types × 2 autopilot_modes = 6 paths
```

**Step 3:** Map each path to observable outcome
```
(youtube_url, autopilot=false) → Step 2
(youtube_url, autopilot=true)  → Queue
```

**Step 4:** Each distinct outcome = 1 AC

---

## 4. Path Prioritization (Pareto)

| Tier | Path Type | Rule | Coverage |
|------|-----------|------|----------|
| **1 (Always)** | Happy | All valid input combinations | ~80% of users |
| **2 (High value)** | Sad | Critical field validation only | Common errors |
| **3 (Selective)** | Error | Data-mutating operations only | High-impact failures |

**What to skip (80/20):**
- Sad paths for optional fields
- Error paths for read-only operations
- Edge cases requiring complex setup

---

## 5. Given-When-Then Format

### Best Practices

| Do | Don't |
|----|-------|
| Use business language | Reference UI element IDs |
| Focus on observable outcomes | Test database state directly |
| Keep <10 steps per scenario | Include implementation details |
| Be specific ("within 3 seconds") | Be vague ("fast", "good") |
| One behavior per scenario | Test multiple behaviors together |
| Write declaratively | Write imperatively ("click", "verify") |

### Example

**Bad:**
```gherkin
Given the database is running
When I POST to /api/auth with username="test"
Then a JWT token should be generated
```

**Good:**
```gherkin
Given I am on the login page
When I enter valid credentials
Then I should see the dashboard
And I should see a welcome message
```

**Sources:** [Cucumber](https://cucumber.io/docs/bdd/better-gherkin/), [Martin Fowler](https://martinfowler.com/bliki/GivenWhenThen.html)

---

## 6. Storage: Local Tracking File

### Location
```
.claude/tracking/issue-{N}.md
```

### Template
```markdown
# Issue #{N}: {title}

## Definition of Done
- [ ] Item 1
- [ ] Item 2

## Acceptance Criteria
### AC1: {scenario name}
Given: ...
When: ...
Then: ...
```

### Why local file over GitHub issue body?

| Aspect | Issue Body | Local File |
|--------|-----------|------------|
| Editing | Full body rewrite | Direct file edit |
| Diff visibility | Hidden in "edited" | Git diff shows changes |
| Branching | N/A | AC can branch with worktree |
| Versioning | Manual | Git history |

### Rules
- **Updates:** Edit file → commit → push immediately
- **Issue body:** Links to GitHub URL (not local path)
- **Versioning:** Git history = version history (no changelog needed)

---

## 7. Skill Responsibilities

| Skill | Responsibility |
|-------|---------------|
| **deliverable-tracking** | Create issue + tracking file (with initial DoD) |
| **onboarding** | Verify file exists, check for AC, prompt rubber-duck if missing |
| **rubber-duck** | Where DoD/AC is refined through conversation |
| **ac-create** | AC methodology and best practices |

### Workflow

```
deliverable-tracking (Issue Creation)
└── Creates issue + tracking file with DoD
    ↓
onboarding (Session Start)
├── Verifies tracking file exists
├── Checks for AC section
└── IF no AC → prompts: "Run /rubber-duck to create AC?"
    ↓
rubber-duck (AC Creation)
├── Invokes AC methodology
├── Defines Given-When-Then scenarios
└── Updates tracking file
    ↓
worktree (Implementation)
└── Implements against DoD + AC
```

### Migration (Existing Issues)

When onboarding encounters an issue without a tracking file:
1. Prompt user: "Tracking file missing. Create from issue DoD?"
2. If yes: Pull DoD from issue body → create file → commit → push
3. Continue to AC check

---

## 8. Timing

| Element | When Created | Can Be Refined |
|---------|--------------|----------------|
| **DoD** | Issue creation (deliverable-tracking) | Yes (rubber-duck) |
| **AC** | Before implementation (rubber-duck) | Yes (as understanding grows) |

**Key insight:** 40% of early-written AC need revision. JIT creation (just before implementation) produces higher quality AC.

---

## 9. Issue Body Format

### New Format (Minimal)

```markdown
## What?
{description}

## Why?
{motivation}

## Tracking
[DoD + AC](https://github.com/{owner}/{repo}/blob/main/.claude/tracking/issue-{N}.md)

## Notes
{optional context}
```

All details live in tracking file. Issue body is a pointer.

---

## 10. Future Work

- [ ] Design Verification Log format and workflow (tracked in #89)
- [ ] Create `/ac-create` skill with methodology
- [ ] Update `deliverable-tracking` to create tracking file
- [ ] Update `onboarding` to verify tracking file + AC

---

## Sources

- [Scrum.org: DoD vs AC](https://www.scrum.org/resources/blog/what-difference-between-definition-done-and-acceptance-criteria)
- [Cucumber: Better Gherkin](https://cucumber.io/docs/bdd/better-gherkin/)
- [Martin Fowler: Given-When-Then](https://martinfowler.com/bliki/GivenWhenThen.html)
- [Mike Cohn: Testing Pyramid](https://www.agilecoachjournal.com/2014-01-28/the-agile-testing-pyramid)
- [AltexSoft: AC Best Practices](https://www.altexsoft.com/blog/acceptance-criteria-purposes-formats-and-best-practices/)
