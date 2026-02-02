---
publish: true
---

# Meeting Agenda: Step 1 - Hierarchy Understanding
[[client-archibus]]

## Meeting Goal

Facilitated discovery session to understand how implementers determine client hierarchy. By the end of this session, we should have:

1. **Process clarity** - How hierarchy is determined today (manual process)
2. **Variation handling** - How to choose between hierarchy options
3. **Challenge awareness** - Potential issues AI will face

## Pre-Read

Please review the design document before the meeting:

[Chain 1B: AI Bulk Data Entry - Design Document](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)

---

## Discussion Topics

### 1. Context Recap

Brief orientation: We're building AI-assisted bulk data entry. The 3-step process is:
1. **Understand hierarchy** ← This meeting
2. Map input schema to Bruce BEM schema
3. Fill level-by-level

This meeting focuses on Step 1: how does AI understand which hierarchy a client uses?

### 2. Current Process

*Conversation prompt:*

> Miguel suggested something like draggable cards to capture hierarchy - how do you see this working? How have you determined hierarchy manually in past implementations?

**Goal:** Let participants express how they think about hierarchy determination. Not looking for "the answer" - looking for how they articulate the process.

### 3. Handling Variations

The hierarchy options are defined (9-level max: Campus → Site → Complex → Property → Building → Floor → Stairwell → Suite → Room), but clients use different subsets.

**Questions:**
- How should AI present the hierarchy choice to users?
- What makes one hierarchy option "right" for a client vs another?
- What signals indicate which levels a client actually uses?

### 4. Potential Challenges

What challenges do you anticipate AI will face when determining hierarchy?

*Note: Human challenges are likely AI challenges. Better to surface them now than during execution.*

### 5. Next Steps

- What follow-up is needed after this conversation?
- Any documentation or examples that would help?

---

## Meeting Format

- **Type:** Facilitated discovery
- **Expectation:** One-off session - gather input, then proceed independently with Ryan
- **Outcome:** Enough understanding to design AI's hierarchy capture flow

---

## Related

- **Issue:** [#373 - AI Bulk Data Entry Prototype](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Schema Reference:** [Asset Import Schema (SharePoint)](https://aschs-my.sharepoint.com/:x:/g/personal/rein_suurvali_asc-hs_com/IQCZWnERRmHxRb3r4bb3BZzRAfjwreav4FEP85btLlaVWNI)
