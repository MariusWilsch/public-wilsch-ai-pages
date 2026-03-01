---
publish: true
---

# IITR Wartungsvertrag — Ulrich Prep Meeting

[[client-iitr]]

## Meeting Goal

Resolve two open Wartungsvertrag terms before sending the financial framework to IITR:

1. **Scope boundary** — define which infrastructure layers Marius covers vs. Primeline/Hetzner
2. **Weekend pricing** — decide whether to offer extended-hours availability and at what fee

Both decisions directly affect the Wartungsvertrag document. Without them, the SLA scope
and standby fee are incomplete.

## Pre-Read

- [IITR Contract Extension — Commercial Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-contract-extension-framework) — pricing decisions already made (120 EUR/h, 300 EUR standby, Festpreis per project). Focus on the two **Undefined** markers in Sections 3 and 3.
- [CTS Solutions — IT SLA Preisliste](https://cts-solutions.at/edv-preisliste-wartungsvertrage-slas/) — reference pricing for extended-hours and weekend SLA tiers

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Betreuung scope boundary and liability
⏱️ 10 min

The Wartungsvertrag says "dedizierte Betreuung unseres Servers" — but four infrastructure
layers exist, each with a different owner:

| Layer | Current owner | Marius's role |
|-------|--------------|---------------|
| **Application** (AI code, Docker, Ollama) | Marius | Builds and maintains |
| **Middleware** (OS services, networking) | Unclear | Manages in practice |
| **Hardware** (Staging: Hetzner bare metal; Production: Primeline) | Hetzner / Primeline | No ownership |
| **Network** (Hetzner 99% SLA; Primeline 4h response Mon–Fri) | Hetzner / Primeline | No control |

- Marius's current approach: handle everything he can, escalate what exceeds his capability
- Primeline's production warranty covers hardware replacement (36-month, 4h response) — but only Mon–Fri
- If Primeline is slow during a hardware failure, IITR is offline. The contract language determines whether that's Marius's liability or not
- Ulrich flagged this in the Feb 27 call as "a huge risk" if the contract doesn't draw a clear boundary

**To resolve:** The contract liability boundary — which layers Marius is responsible for, and explicit exclusion language for third-party infrastructure failures.

### 2. Weekend SLA coverage pricing
⏱️ 10 min

The current Wartungsvertrag proposes 24h reaction during German business hours (Mon–Fri
8–18 CET). Worst case: IITR reports a failure Friday at 17:01, Marius responds Monday
at 17:00 — a 72-hour gap.

Marius works every day in practice, so extending availability is operationally feasible.
The question is how to price it.

| Coverage tier | Reaction window | Market rate (single server) | Current proposal |
|---------------|----------------|---------------------------|-----------------|
| **Business hours** (Mon–Fri 8–18) | 24h within business days | 300–330 EUR/month | 300 EUR ✓ |
| **Extended hours** (Mon–Fri 7–20) | 24h within extended days | ~450 EUR/month | — |
| **5/24** (Mon–Fri, any hour) | 24h any weekday | ~690 EUR/month | — |
| **7/24** (every day, any hour) | 24h any day | ~890 EUR/month | — |

- Market reference: CTS Solutions Austria published SLA tiers per Linux server
- At 120 EUR/h, the current 300 EUR standby equals 2.5 hours — a single weekend incident could consume that
- Marius is willing but wants the price to reflect the personal commitment

**To resolve:** Which availability tier to offer IITR and the corresponding standby fee — balancing Marius's willingness with fair compensation for extended coverage.

## Meeting Format

- **Type:** Decision call (Marius × Ulrich)
- **Duration:** 20 min
- **Expectation:** Ulrich reads the Commercial Framework design doc beforehand — pricing decisions are already made, only the two Undefined items need his input
- **Outcome:** Two decisions that complete the Wartungsvertrag scope and pricing — ready to draft the contract document for Eckhard

## Related

- **Issue:** [#954 — Prepare extension contract for signing](https://github.com/DaveX2001/deliverable-tracking/issues/954)
- **Design Doc:** [IITR Contract Extension — Commercial Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-contract-extension-framework)
- **Transcript:** [Marius × Ulrich — Contract Extension Pricing Strategy (Feb 27)](https://app.fireflies.ai/view/01KJEVQ0Q6XWZNZP9P1FTPB2VW)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/9f14b021-2a4f-4da9-8925-b30c9eb7d426.jsonl
