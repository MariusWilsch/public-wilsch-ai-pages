---
publish: true
---

# EasyBooks — SA-Stafford Alignment Meeting
[[easybooks-meeting-agenda-stafford]]

## Meeting Goal

Resolve four open design questions from the EasyBooks v0.2 design doc before the RFP response can be finalized. Each topic is marked **Undefined** in the design doc and requires SA decision or SA-Stafford alignment.

1. **Team model transparency** — whether to communicate the AI-augmented delivery model to Stafford
2. **Commercial terms** — retainer, success fee, space fee structure
3. **Proof of capability** — which projects to showcase in the RFP response
4. **Cost framing** — build costs, running costs, pricing model for the response

## Pre-Read

- [EasyBooks Design Doc v0.2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/easybooks-design-doc) — Part 4 (Delivery), Part 7 (Phasing), Part 9 (Proof)
- [Contract Strategy Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/contract-strategy-retainer-model-design) — Three-Slice Ownership Model
- Stafford's RFP — [#874 comment](https://github.com/DaveX2001/deliverable-tracking/issues/874#issuecomment-2707966123) with 2x docx attachments

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. AI-augmented team model — reveal or internal
⏱️ 10 min

Stafford does not know that the team operates with AI as a force multiplier. In both transcripts (Feb 18 + Feb 20), he compares our pricing against traditional freelancers ($1,500-$3,000 per build). No mention of Claude Code, force multiplication, or AI-augmented development was made in any conversation.

- The "Fractional AI Department" pitch from #847 positions AI as the competitive advantage — but that pitch was designed for the service model, not necessarily for every client
- Explaining the model differentiates from the $1,500 freelancers he's comparing against
- Not explaining means delivering faster than expected without revealing how

**To resolve:** Whether the AI force multiplier model is part of the Stafford pitch or remains an internal operational advantage.

### 2. Commercial terms — retainer, success fee, space fee
⏱️ 20 min

No contract exists. Negotiation is ongoing. The Feb 21 position was "tending towards no" (retainer too low at 3.5K). The Feb 22 position shifted to "retainer + space fee for three months." Stafford responded with a formal RFP on Mar 8, suggesting the deal is alive.

- The design doc deliberately contains no numbers — commercial terms are SA authority
- The Three-Slice Ownership Model applies: Wilsch AI owns the function, Stafford owns the results
- Base + success (AND not OR) is the standard from #847
- 3-month initial commitment, renewable if both sides happy

**To resolve:** The commercial structure (retainer amount, success fee percentage, space fee, payment terms) that Marius brings to Stafford.

### 3. Proof of capability — project selection for RFP Section D
⏱️ 15 min

Stafford's RFP requests specific examples across four categories: AI-agent systems, growth/automation, document generation, and audio/transcription. Confidentiality constraints apply to most client projects.

- Paul Faceless YouTube is a candidate (content generation + automation pipeline)
- Each example needs: what was built, role, team size, timeline, budget range, what worked/what you'd change
- The design doc has a placeholder table in Part 9 with "TBD — SA selects" entries
- Traceline itself could serve as proof of the methodology

**To resolve:** Which VeloxForce projects are shareable and map to Stafford's four capability categories.

### 4. Cost framing — build costs, running costs, pricing for the response
⏱️ 15 min

Stafford explicitly requests build cost estimates by phase and ongoing running costs (hosting, storage, LLM inference, transcription). The design doc contains no cost numbers by design — all routed here.

- Build cost is bundled in the retainer model — not itemized per engine
- Running costs (hosting, LLM inference, storage) are separable and Stafford needs to understand them regardless of engagement model
- The design doc proposes 3 phases: MVP (weeks 1-6), V1 (weeks 7-12), V2 (post-retainer)

**To resolve:** What cost information appears in the Stafford-facing response — retainer framing, running cost estimates, or both.

## Meeting Format

- **Type:** SA review + negotiation prep
- **Expectation:** SA reviews design doc v0.2 holistically (Speechify), brings commercial position
- **Outcome:** Four Undefined markers resolved → design doc updated → RFP response can be drafted

## Related

- **Issue:** [#1078 — JA Design Doc Pass 1](https://github.com/DaveX2001/deliverable-tracking/issues/1078)
- **Epic:** [#874 — Livebook Pre-Sales](https://github.com/DaveX2001/deliverable-tracking/issues/874)
- **Design Doc:** [EasyBooks Design Doc v0.2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/easybooks-design-doc)
