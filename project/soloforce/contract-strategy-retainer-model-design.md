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

**Undefined:** The standard first engagement format that "proves capability" before the retainer conversation — fixed-price project, prototype, workshop, or does it vary by client type?

The system is person-independent by design. Contracts sell the delivery system, not the individual.

The system-dependent model is the structural counter to every trust-deficit objection. "I've been burned before" is person-dependent thinking — the person left, the capability left. In a system-dependent engagement, the delivery infrastructure stays: documented processes, AI agent configurations, and observable audit trails remain regardless of which operator runs them. The E-Myth Revisited (Gerber, 1986) identified this forty years ago — businesses fail when they depend on the technician instead of the system. AI-native delivery makes the franchise prototype real: build the system once, run it repeatedly, any operator can execute.

**The organizational shift:** Workers don't lose jobs — they shift upward. Each person becomes a 1-to-N manager of AI agents instead of doing the work manually. Institutional knowledge stays in the system even when people leave — because the AI does the work, the knowledge is encoded in configurations and processes, not in someone's head. "AI doesn't need more tech people — it needs more operational people and org people."

<img src="assets/org-chart-before-after.png" alt="Before/After: Traditional org chart vs AI-Native org chart" style="max-width:100%;height:auto;">

External validation: Sierra AI ($4.5B), WPP, and Decagon (YC-backed) have moved from hourly to outcome-based pricing. 67% of consulting buyers prefer fixed-fee over T&M (Deloitte 2024). [Mayer Brown published a contract framework for agentic AI delivery (February 2026)](https://www.mayerbrown.com/en/insights/publications/2026/02/contracting-for-agentic-ai-solutions-shifting-the-model-from-saas-to-services). YC's Spring 2026 RFS codifies this as ["AI-Native Agencies"](https://www.ycombinator.com/rfs#ai-powered-agencies) — agencies that "use AI internally and sell finished outcomes at software-level margins. They'll scale far bigger than any agencies that exist in these fragmented markets today." [Sourcegraph's FY26 org chart](https://sourcegraph.com/blog/revenge-of-the-junior-developer) shows the new hierarchy: Senior Leaders → Managers → Developers → AI Managers → AI Coding Agents.

### Part 2: The Value Proposition — Outsourced AI Department

The client-facing framing: "You're outsourcing your AI department to me. I own the function, you own the results."

But "outsourcing" is only half the picture. Wilsch AI also builds AI capability inside the client's company — making their teams more efficient, introducing them to what's possible. Over time, the client gains internal AI competence. This isn't a threat to the engagement: the Claude Code Operating System improves faster than the client's internal capability grows. The gap between what they can do alone and what the orchestrated system delivers keeps widening — not shrinking. And every time the client sees new possibilities, they come back asking for more.

Every company needs AI capability — it's becoming as foundational as marketing or IT. Most Mittelstand clients don't have it and can't build it internally. Wilsch AI fills this function: not just building things, but bringing the AI knowledge, methodology, and orchestration capability that enables the client to act on AI at all. AVO demonstrated this in the first workshop — the production department was instantly interested in AI, even though the engagement wasn't scoped for them. Cross-functional demand the client didn't anticipate. The possibility space, once visible, creates its own demand across departments. Clients always ask: "Can you do this for our other departments too?" — that obligation is the proof the model works.

Department framing works because:
- It implies a team behind the delivery (which exists — the AI agent system)
- It scales as client needs grow
- Mittelstand clients already outsource accounting, IT, and legal — AI department is the next logical outsource
- It positions the retainer as operating cost (Betriebskosten), not project cost — different budget category, different approval logic

The value delivered is alignment — understanding the client's problem before writing code. The design doc process is the visible differentiator. The Claude Code Operating System that makes delivery fast and reliable stays proprietary.

Every engagement owns a function, not a task list. The function stays constant; deliverables within it change. A contractor who "builds for 3K and leaves" delivers a thing, not an ongoing function. When they leave, the function goes empty.

The AI department is unique among outsourced functions: it touches every other department. Marketing, operations, finance, customer service — AI capability has cross-cutting impact. "Every employee you have is an AI use case." This makes the outsourced AI department fundamentally more valuable than outsourced accounting or IT — it multiplies the effectiveness of everything else the company does.

Like ARK Invest's "Technological Convergence" thesis ([Big Ideas 2026, p.5](https://ark-invest.com/big-ideas-2026)) — where AI is the central node touching all other technologies — the AI department is the central node touching all other departments. When one department sees what AI can do, the others demand it. AVO demonstrated this: the first workshop was scoped for software, but the production department was instantly interested. The possibility space, once visible, creates its own demand across departments. Clients always ask: "Can you do this for our other departments too?"

<img src="assets/ai-department-convergence.png" alt="AI Department convergence — the department that touches every other department" style="max-width:100%;height:auto;">

**Observability replaces timesheets.** The AI delivery system produces a complete audit trail — every agent action, every conversation, every decision is queryable. Point-in-time observability of what your AI department has been doing replaces the need for Stundennachweis. The system IS the transparency layer — more transparent than any person could be, and it comes free as a byproduct of AI-native delivery.

**Undefined:** How to frame the "AI touches everything" value proposition concretely for Mittelstand clients in the flyer — the convergence visual exists, the pitch narrative needs empirical testing with real clients.

**Undefined:** The Betriebskosten vs Projektkosten budget psychology — clients spend €150K on marketing without blinking but resist €3K/month retainers because the retainer is framed as project cost, not operating cost. The department framing moves the retainer into the operating budget where it gets renewed, not re-justified. This connects to the contract lifecycle: as the AI function decreases the client's operating costs or COGS, the success component grows from real savings — the client pays less base, more success, and both sides win. Needs deeper probing on the mechanics.

The contract communicates: methodology architect with concurrent engagements, not embedded founder or dedicated CTO. Distance principle: deliver the system from outside, not embedded inside the company.

### Part 3: The Pricing Structure — Base + Success, Always AND

Every retainer has two components, always combined:
- **Base fee:** Bridge cost — buys system access, not person-time. Metered by hours of throughput (5h, 10h, 20h tiers) but what the client buys is the AI delivery system plus an operator, not a specific individual's attention. The base is the guaranteed floor; the real money comes from the success component.
- **Success component:** Skin in the game — a percentage of the commercial outcome the work enables, tied to net margin not gross revenue. The aim is to make the real money here, not on the base.

This is AND, not OR. The base ensures system access; the success component aligns incentives. The price anchor for clients: "You can hire a person to deliver a deliverable, or you can subscribe to an ongoing AI-native agency that delivers monthly outcomes." The comparison is hiring internally (€70-80K+ for one full-time AI person) versus subscribing to an AI department at a fraction of the cost — with the system scaling as needs grow.

The success component is margin-dependent and negotiated per deal:
- Product engagements (Archibus): revenue share from subscriptions
- Sales engagements (UWI): share of projects sold from joint effort
- The percentage depends on the client's margins and what is being sold

Scope is defined as function ownership — what position does the provider fill? — not as a task catalog. Scope can be defined after the first 1-2 months, formalized at the first re-evaluation.

Outcome reporting replaces timesheets: weekly problem-solution pairs with business value framing, linked to design docs and executive summaries.

**Undefined:** Revenue share mechanics — standard structure, tiers, caps.
**Undefined:** Weekly outcome report format — needs empirical testing (UWI as first implementation).

The success fee also acts as a client selection filter: if the client cannot demonstrate commercial viability sufficient for a meaningful success component, the engagement may not be a fit. This inverts the negotiation — the client proves their business case, not just the provider justifying the fee.

**Undefined:** How to frame the success fee conversation as a mutual qualification step during contract negotiation — the mechanics of using the success fee as a client filter.

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

**Non-exclusive:** Explicitly stated. The theory: non-exclusivity enables lower per-client pricing because the overbooking model distributes capacity across 3-4 concurrent engagements, with total revenue exceeding what a single exclusive engagement would yield.

**Undefined:** The non-exclusivity pricing math needs validation with real numbers — does lower per-client price × 3-4 concurrent engagements actually increase or decrease margins compared to higher per-client price × 1 exclusive engagement? This is theory until tested against actual client revenue data.

**Internal cascade:** The same base + success structure applies to team members internally — the model works at every level of the delivery chain.

**Contract lifecycle:** The base retainer bridges the period before the success fee generates meaningful income. Over time, as the AI function establishes itself and the client generates revenue from it, the success component grows and the base can decrease. The ideal trajectory: success fee outcompetes the retainer — the client pays less in fixed cost and more from shared success. The retainer is always a cost the client bears. The success fee is earned value both sides celebrate — proof that the AI function is generating real returns.

**Undefined:** When the success fee outcompetes the base, it means the client has a new revenue stream that didn't exist before — the ultimate proof of value. The mechanics and success criteria of this lifecycle transition need deeper probing.

---

## Source

**Transcripts:**
- [Collaborator negotiation (2026-02-20)](https://app.fireflies.ai/view/01KHXJSM63VJB0JT88KS8E4NZJ) — retainer + success fee, skin-in-the-game, client inhibitions
- [Dad <> Marius (2026-02-18)](https://app.fireflies.ai/view/01KHQHYP17N1DRR9183J3BVCFR) — scope definition, financial pressure, outcome-based delivery
- [David sync (2026-02-18)](https://app.fireflies.ai/view/01KHR8XQMWMBV1TJJF4GVNA3RK) — revenue baseline
- [Archibus post-demo (2025-10-08)](https://app.fireflies.ai/view/01K71QRZX71S801A7ENSAMS301) — Swiss watch, "I want you in my team"
- [Doru/Needed Tech (2026-02-21)](https://app.fireflies.ai/view/01KHZGZN8JG04A8JP1K0PSJZDX) — positive case: training program entry, AI department pitch, zero hard objections
- [Doru blocker (2026-02-21)](https://app.fireflies.ai/view/01KHZGZN8JG04A8JP1K0PSJZDX) — quoting automation, resale opportunity, "every employee is an AI use case"

**Artifacts:**
- [Archibus Retainer Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/archibus-retainer-services-agreement-6mo-extension)
- [UWI Supportvertrag](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/supportvertrag-dezember-2025)
- [UWI Consolidation Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-contract-consolidation)
- [Strategic Objective](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
- [Organization Chart](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
- [Sierra AI — Outcome-based pricing](https://sierra.ai/blog/outcome-based-pricing-for-ai-agents)
- [Mayer Brown — Contracting for Agentic AI (Feb 2026)](https://www.mayerbrown.com/en/insights/publications/2026/02/contracting-for-agentic-ai-solutions-shifting-the-model-from-saas-to-services)
- [ARK Invest — AI Great Acceleration (2026)](https://www.investmentnews.com/equities/cathie-woods-ark-invest-predicts-ai-other-techs-great-acceleration/264929) — AI as the technology that touches all other technologies
- [ARK Invest — Big Ideas 2026, p.5](https://ark-invest.com/big-ideas-2026) — "Technological Convergence Is Accelerating" — source for department convergence diagram
- [YC RFS Spring 2026 — AI-Native Agencies](https://www.ycombinator.com/rfs#ai-powered-agencies) — "agencies of the future will look more like software companies, with software margins"
- [Sourcegraph — Revenge of the Junior Developer](https://sourcegraph.com/blog/revenge-of-the-junior-developer) — FY26 org chart: Devs → AI Managers → AI Coding Agents
- E-Myth Revisited (Michael Gerber, 1986) — franchise prototype model applied to AI-native delivery
- **Competitors:** [tune.eco](https://tune.eco) (SaaS agents, €149/user/mo), [heronos.com](https://heronos.com) (digital Fachkräfte, €375/mo), [mindgpt.cloud](https://mindgpt.cloud) (private AI workspace) — all sell SOFTWARE, not a FUNCTION

**Sessions:**
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/87151f23-d09a-4ed4-a4fe-71368c49c52d.jsonl` — position-as-product thesis
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/15c30f47-6bd1-45c6-8e18-3295b58d0cb8.jsonl` — first extraction pass
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/9715c300-2036-4389-882d-e9a3af2eb55a.jsonl` — second extraction pass (this session)
