# Organizational Strategy Lifecycle
[[life-vision]]

---
publish: true
---

The lifecycle for building organizational structure that runs without the founder. Based on Michael Gerber's *The E-Myth Revisited*, Chapter 14.

## Core Principle

**Organize around positions, not personalities.**

When everybody's accountable for everything, nobody's accountable for anything. The Organization Chart defines positions (boxes) with clear accountability, regardless of who fills them.

---

## Conceptual Relationships

```
Organization Chart (1 per company)
        │
        │ contains (1:N)
        ▼
    Positions (N boxes)
        │
        │ each has (1:1)
        ▼
Position Contracts (accountability documents)
        │
        │ references (1:1)
        ▼
Operations Manuals (documented systems)
```

| Relationship | Cardinality | Description |
|--------------|-------------|-------------|
| Org Chart → Positions | 1 : N | One chart contains many boxes |
| Position → Contract | 1 : 1 | Each position has one contract |
| Position → Manual | 1 : 1 | Each position has one how-to guide |
| Person → Positions | 1 : N | One person can fill many boxes (initially) |

---

## Full Lifecycle

```
╔══════════════════════════════════════════════════════════════════════════════╗
║              ORGANIZATIONAL STRATEGY LIFECYCLE                               ║
║                    (One Position at a Time)                                  ║
╠══════════════════════════════════════════════════════════════════════════════╣
║                                                                              ║
║  STAGE 1: STRUCTURE                                                          ║
║  ┌────────────────────────────────────────────────────────────┐              ║
║  │ Activity: Design the organization when "done"              │              ║
║  │ Output:   ORGANIZATION CHART (1 per company)               │ ◄── Artifact║
║  │           └── Contains N position boxes                    │              ║
║  │                                                            │              ║
║  │ For each position:                                         │              ║
║  │ Output:   POSITION CONTRACT (1 per position)               │ ◄── Artifact║
║  │           └── Results, standards, accountability           │              ║
║  └────────────────────────────────────────────────────────────┘              ║
║                              │                                               ║
║                              │ You sign the contract (take the position)     ║
║                              ▼                                               ║
║  STAGE 2: PROTOTYPE THE POSITION                                             ║
║  ┌────────────────────────────────────────────────────────────┐              ║
║  │ Activity: Work IN the position while working ON it         │              ║
║  │ Process:  BDP LOOP (ongoing, not a one-time event)         │ ◄── Process ║
║  │           ┌──────────────────────────────────────┐         │              ║
║  │           │  Innovate → Quantify → Orchestrate   │         │              ║
║  │           │       ▲                    │         │         │              ║
║  │           │       └────────────────────┘         │         │              ║
║  │           │       (loop until system stable)     │         │              ║
║  │           └──────────────────────────────────────┘         │              ║
║  │                                                            │              ║
║  │ Output:   OPERATIONS MANUAL (1 per position)               │ ◄── Artifact║
║  │           └── Documented system: "how we do it here"       │              ║
║  └────────────────────────────────────────────────────────────┘              ║
║                              │                                               ║
║                              │ Manual complete enough to hand off            ║
║                              ▼                                               ║
║  STAGE 3: REPLACE YOURSELF                                                   ║
║  ┌────────────────────────────────────────────────────────────┐              ║
║  │ PHASE A: Before Hire                                       │              ║
║  │ - You own: Tactical (doing) + Strategic (prototyping)      │              ║
║  │                                                            │              ║
║  │ PHASE B: During Transition                                 │              ║
║  │ - Hire signs Position Contract                             │              ║
║  │ - Hire learns Operations Manual                            │              ║
║  │ - You: Training, oversight, ensuring system works          │              ║
║  │ - Process: TRAINING METHOD                                 │ ◄── Process ║
║  │   └── Daily syncs, issues, hard cap for independence       │              ║
║  │                                                            │              ║
║  │ PHASE C: Fully Transitioned                                │              ║
║  │ - Hire owns tactical work + can suggest improvements       │              ║
║  │ - You move UP to position above on org chart               │              ║
║  └────────────────────────────────────────────────────────────┘              ║
║                              │                                               ║
║                              │ Position filled, you're now one level up      ║
║                              ▼                                               ║
║  STAGE 4: REPEAT (Meta-Pattern)                                              ║
║  ┌────────────────────────────────────────────────────────────┐              ║
║  │ You now hold the position ABOVE the one you just filled    │              ║
║  │                                                            │              ║
║  │ Go back to Stage 2: Prototype THIS new position            │              ║
║  │ - New tactical work (managing vs doing)                    │              ║
║  │ - New strategic work (prototyping THIS role)               │              ║
║  │ - New Operations Manual for THIS position                  │              ║
║  │                                                            │              ║
║  │ Continue until you reach: SHAREHOLDER (outside business)   │              ║
║  │ - No tactical work                                         │              ║
║  │ - Strategic oversight only (is COO hitting objectives?)    │              ║
║  └────────────────────────────────────────────────────────────┘              ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

---

## Key Definitions

### Position
A box on the Organization Chart. Defined by:
- **Accountability:** What results this position must produce
- **Reporting:** Which position it reports to
- **Authority:** What decisions can be made

**Test:** "If this position is empty, what results stop being produced?"

### Position Contract
Not a job description (tasks). A **contract** (results):
- Summary of results to be achieved
- Standards by which results are evaluated
- Signature line for accountability commitment

Position Contracts are long-lived because they define *results*, not tasks.

### Tactical vs Strategic Work
- **Tactical:** Doing the work IN a position (making sales calls)
- **Strategic:** Working ON a position (prototyping the process, improving the system)

Each position has BOTH tactical and strategic work. Moving up doesn't eliminate strategic work - it shifts to the new level.

### Operations Manual
The documented system for HOW to achieve position results:
- Created through BDP loop (Innovate → Quantify → Orchestrate)
- "This is how we do it here"
- Enables replacement: hand manual to new hire, they run the system

---

## Artifact Summary

| Artifact | Cardinality | Description |
|----------|-------------|-------------|
| Organization Chart | 1 per company | Visual structure: positions + reporting lines |
| Position Contract | 1 per position | Results, standards, accountability |
| Operations Manual | 1 per position | Documented system for the position |

## Process Summary

| Process | Runs When | Description |
|---------|-----------|-------------|
| BDP Loop | Continuous while prototyping | Innovate → Quantify → Orchestrate |
| Training Method | During transition | Daily syncs, issues, independence criteria |

---

## Transition Lifecycle

```
Before Hire:
  Position: Developer
  You: Tactical (coding) + Strategic (documenting process)

During Transition:
  Position: Developer → Hire doing tactical, you training
  Position: Dev Lead → You now doing tactical + strategic HERE

Fully Transitioned:
  Position: Developer → Hire owns it (tactical + suggests improvements)
  Position: Dev Lead → You own tactical + strategic, prototyping THIS
```

The pattern repeats up the chart until you're only **Shareholder** - outside the business, owning no tactical work.

---

## Example Organization Chart Structure

```
═══════════════════════════════════════════════════════════════
                    SHAREHOLDERS
                      (Owner)
═══════════════════════════════════════════════════════════════
                         │
                         ▼
                 ┌───────────────┐
                 │      COO      │
                 │   (President) │
                 └───────┬───────┘
                         │
         ┌───────────────┼───────────────┐
         │               │               │
         ▼               ▼               ▼
┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│ VP/Product  │  │VP/Operations│  │ VP/Finance  │
└──────┬──────┘  └──────┬──────┘  └─────────────┘
       │                │
       ▼                ▼
┌─────────────┐  ┌─────────────┐
│Client Mgmt  │  │  Dev Lead   │
└─────────────┘  └──────┬──────┘
                        │
                        ▼
                ┌─────────────┐
                │  Developer  │
                └─────────────┘
```

**Reading the chart:**
- Shareholders sit ABOVE the business (outside)
- COO is the first position INSIDE - accountable to shareholders
- You START at the bottom, prototype each position, and climb UP

---

## Related Documents

- [Primary Aim - Life Vision](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/primary-aim-life-vision)
- [Strategic Objective - Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
- [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence)

---

## Source

- E-Myth Organizational Strategy interview (2026-02-04)
- Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 14
