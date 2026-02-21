---
publish: true
---

# Contract Strategy & Retainer Model Design
[[project-index-847]]

Design doc for how Wilsch AI Services structures contracts, retainers, and pricing to align with an AI-native delivery model.

---

## Problem Statement

Wilsch AI Services operates as an AI-native agency where the Claude Code Operating System orchestrates 3-4 concurrent Claude Code terminal sessions for client engagements. The system handles AI behavior deterministically — operators focus on the task at hand, not on making AI work correctly. These terminals can serve different clients simultaneously or multiple workstreams within the same project. This enables overbooking one person by 2-3x, and non-interactive autonomous agents will deepen this multiplier further.

The current contract landscape has two problems: hourly contracts that create false transparency (Abtmayr-Reichert, IITR, UWI's timesheet requirement), and no standard model for new engagements. Retainers (Archibus at €3K/month) and fixed-price projects (AVO, Rekers) both work within the AI-native model — neither requires hourly attribution. Hourly is the specific enemy: it is structurally incompatible with a delivery model where maker time bundles 3-4 client contexts in a single block.

The core problem: hourly contracts force a traditional services framing (hours traded for money) onto AI-orchestrated throughput where per-client hour attribution is impossible. Both retainer and fixed-price are valid alternatives — the goal is to eliminate hourly entirely and standardize around outcome-based models.

This misalignment creates four failures:
1. **Pricing undervalues delivery** — clients pay per-hour rates while receiving AI-department-level throughput that overbooking makes 2-3x more efficient
2. **Scope definitions constrain the wrong thing** — task lists instead of function ownership
3. **No standard model exists** — each client negotiation starts from scratch
4. **Clients buy deliverables instead of outcomes** — leads and new clients expect a build-and-leave project, not ongoing function ownership

This is not a unique problem. Y Combinator's Spring 2026 RFS identifies "AI-Native Agencies" as a category: agencies that use AI internally and sell finished outcomes at software-level margins instead of billing hours. Traditional agencies add people to grow; AI-native agencies scale throughput. The contract model must reflect this shift.

**Preconditions:**
- The Claude Code Operating System enables orchestration of 3-4 concurrent client engagements via AI terminals, with operators focusing on delivery, not AI behavior
- The first operator handoff is in progress — proving the system works without the founder
- The Strategic Objective targets €360K principal through a system that runs without the founder — contracts must support person-independent delivery
- German Mittelstand clients think in Pflichtenheft, Supportvertrag, and Stundennachweis — translation into these frames is required

---

## Success Definition

**Goal:** A standardized contract model that sells AI-orchestrated throughput as an outsourced AI department — person-independent, outcome-based, with software-level margins. Fixed-price projects remain a valid path where retainer doesn't fit. The design doc is the engagement entry point — alignment before code.

**Success:**
- New engagements use either retainer (function ownership + outcome reporting) or fixed-price (no hourly, AI speed = better margins)
- Existing hourly clients (UWI, IITR, Abtmayr-Reichert) migrate away from hourly by end of Q1 2026 (March 31)
- Every engagement starts with a design doc that aligns understanding before implementation begins

**Done test:** Can a new operator sign a client on the standard contract without the founder negotiating custom terms? If yes → design is complete.

---

## Approach

### Part 1: The Delivery Model — Why Hourly Must Go

Hourly billing is structurally incompatible with AI-native delivery. Timing data shows maker time interleaving 3-4 client contexts in a single block — attributing hours to individual clients produces false transparency, not accountability. Both retainer and fixed-price work; hourly does not.

The delivery model operates as a "main thread": the human is the CPU scheduler, AI agents are spawned processes per client. Opportunity cost is magnified — every idle moment on the main thread means N agents not running. The client doesn't buy 1/4 of a person's attention; they buy access to an AI-orchestrated delivery team.

What this team delivers maps to the Strategic Objective's value identity: the commodity is finished AI projects, the product is possibility space made tangible, and the proprietary method (Claude Code OS) stays internal. **The contract scopes the commodity; the client pays for the product; the method is the competitive moat.** (See: [Strategic Objective — Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services).)

The unlock pattern: prove capability first (prototype or initial project), then transition to retainer as the natural evolution. Archibus demonstrated this — the client chose retainer because he wanted ongoing strategic access. His words: "I want you in my team. You are a gear in the Swiss watch."

The system is person-independent by design. Contracts sell the delivery system, not the individual.

External validation: Sierra AI ($4.5B), WPP, and Decagon (YC-backed) have moved from hourly to outcome-based pricing. 67% of consulting buyers prefer fixed-fee over T&M (Deloitte 2024). [Mayer Brown published a contract framework for agentic AI delivery (February 2026)](https://www.mayerbrown.com/en/insights/publications/2026/02/contracting-for-agentic-ai-solutions-shifting-the-model-from-saas-to-services).

### Part 2: The Value Proposition — Outsourced AI Department

The client-facing framing: "You're outsourcing your AI department to me. I own the function, you own the results."

Every company needs AI capability — it's becoming as foundational as marketing or IT. Most Mittelstand clients don't have it and can't build it internally. Wilsch AI fills this function: not just building things, but bringing the AI knowledge, methodology, and orchestration capability that enables the client to act on AI at all.

Department framing works because:
- It implies a team behind the delivery (which exists — the AI agent system)
- It scales as client needs grow
- Mittelstand clients already outsource accounting, IT, and legal — AI department is the next logical outsource
- It positions the retainer as operating cost (Betriebskosten), not project cost — different budget category, different approval logic

The value delivered is alignment — understanding the client's problem before writing code. The design doc process is the visible differentiator. The Claude Code Operating System that makes delivery fast and reliable stays proprietary.

Every engagement owns a function, not a task list. The function stays constant; deliverables within it change. A contractor who "builds for 3K and leaves" delivers a thing, not an ongoing function. When they leave, the function goes empty.

The contract communicates: methodology architect with concurrent engagements, not embedded founder or dedicated CTO. Distance principle: deliver the system from outside, not embedded inside the company.

### Part 3: The Pricing Structure — Base + Success, Always AND

Every retainer has two components, always combined:
- **Base fee:** Fair payment for work done — secures the client's capacity slot and guarantees availability
- **Success component:** Skin in the game — a percentage of the commercial outcome the work enables, tied to net margin not gross revenue

This is AND, not OR. The base ensures fair compensation; the success component aligns incentives.

The success component is margin-dependent and negotiated per deal:
- Product engagements (Archibus): revenue share from subscriptions
- Sales engagements (UWI): share of projects sold from joint effort
- The percentage depends on the client's margins and what is being sold

Scope is defined as function ownership — what position does the provider fill? — not as a task catalog. Scope can be defined after the first 1-2 months, formalized at the first re-evaluation.

Outcome reporting replaces timesheets: weekly problem-solution pairs with business value framing, linked to design docs and executive summaries.

**Undefined:** Revenue share mechanics — standard structure, tiers, caps.
**Undefined:** Weekly outcome report format — needs empirical testing (UWI as first implementation).

Fixed-price projects remain valid within the model — no hourly reporting, AI speed means faster delivery equals better margins.

### Part 4: Engagement Types and Migration Path

Three engagement types exist within the model:

- **Retainer** (target): function ownership, outcome reporting, base + success. For clients with ongoing AI needs and joint commercial outcomes.
- **Fixed-price**: scoped deliverable, no hourly reporting, AI speed = margin. For clients who need a specific build without ongoing function ownership.
- **Hourly** (eliminate): structurally incompatible. All hourly engagements migrate to retainer or fixed-price.

Migration principle: existing hourly clients transition at their next contract renewal or re-evaluation. The quarterly fairness review provides the natural transition point.

### Part 5: Contract Mechanics

**Initial term:** 3 months. Long enough to prove value, short enough for client comfort. Renewals move to 6-12 months.

**Quarterly fairness review:** Both sides evaluate whether scope and pricing remain fair. If delivery significantly exceeds the scoped function, the retainer adjusts upward.

**Non-exclusive:** Explicitly stated. Enables lower per-client pricing because the overbooking model distributes capacity across concurrent engagements.

**Internal cascade:** The same base + success structure applies to team members internally — the model works at every level of the delivery chain.

**Contract lifecycle:** The base retainer bridges the period before the success fee generates meaningful income. Over time, as the AI function establishes itself and the client generates revenue from it, the success component grows and the base can decrease. The ideal trajectory: success fee outcompetes the retainer — the client pays less in fixed cost and more from shared success. The retainer is always a "Dorn im Auge" for the client; the success fee is earned value both sides celebrate.

**Undefined:** When the success fee outcompetes the base, it means the client has a new revenue stream that didn't exist before — the ultimate proof of value. The mechanics and success criteria of this lifecycle transition need deeper probing.

---

## Source

**Transcripts:**
- [Collaborator negotiation (2026-02-20)](https://app.fireflies.ai/view/01KHXJSM63VJB0JT88KS8E4NZJ) — retainer + success fee, skin-in-the-game, client inhibitions
- [Dad <> Marius (2026-02-18)](https://app.fireflies.ai/view/01KHQHYP17N1DRR9183J3BVCFR) — scope definition, financial pressure, outcome-based delivery
- [David sync (2026-02-18)](https://app.fireflies.ai/view/01KHR8XQMWMBV1TJJF4GVNA3RK) — revenue baseline
- [Archibus post-demo (2025-10-08)](https://app.fireflies.ai/view/01K71QRZX71S801A7ENSAMS301) — Swiss watch, "I want you in my team"

**Artifacts:**
- [Archibus Retainer Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/archibus-retainer-services-agreement-6mo-extension)
- [UWI Supportvertrag](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/supportvertrag-dezember-2025)
- [UWI Consolidation Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-contract-consolidation)
- [Strategic Objective](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
- [Organization Chart](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
- [Sierra AI — Outcome-based pricing](https://sierra.ai/blog/outcome-based-pricing-for-ai-agents)
- [Mayer Brown — Contracting for Agentic AI (Feb 2026)](https://www.mayerbrown.com/en/insights/publications/2026/02/contracting-for-agentic-ai-solutions-shifting-the-model-from-saas-to-services)

**Sessions:**
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/87151f23-d09a-4ed4-a4fe-71368c49c52d.jsonl` — position-as-product thesis
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/15c30f47-6bd1-45c6-8e18-3295b58d0cb8.jsonl` — this extraction pass
