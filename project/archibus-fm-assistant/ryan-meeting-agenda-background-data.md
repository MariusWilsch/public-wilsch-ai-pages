---
publish: true
---

# Ryan Meeting: Bruce BEM Background Data
[[client-archibus]]

## Success Criterion

After this meeting, both you and Ryan have a shared understanding of:
1. **What information** we're trying to collect
2. **What Bruce BEM calls it** (terminology alignment)
3. **Where to find it** (resources, documentation, people)

You leave with a clear "what to dig into next."

## Pre-Read

[Chain 1B: AI Bulk Data Entry - Design Document](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)

---

## Topic 1: Context (2-min verbal recap)

**Frame:**
> "We're building an AI-assisted bulk data entry system. The problem: partners spend too much time formatting client data for onboarding - it's low-margin work that delays implementations. Our goal: AI fills as much as possible, asks where uncertain, flags what needs human decision. To build this, I need to understand Bruce BEM's data structure - specifically what needs to exist BEFORE we can load asset data."

---

## Topic 2: Terminology

**Frame:**
> "Ian described 'background data' as three buckets: spatial (site→building→floor→room), assets, and people. Each has foreign key dependencies - the precursor must exist before you can add the next level. How does Bruce BEM think about this? Walk me through how you'd describe the prerequisite data."

**Ian's definition (Dec 30):**
- **Spatial drill-down:** Site → Building → Floor → Room → Desk
- **Asset systems:** Follow similar hierarchical patterns
- **People data:** Department → Division → hierarchy
- **Key constraint:** "If you try and input into those tables without the background data being there, it'll just throw it out."

---

## Topic 3: Scope

**Frame (two parts):**

1. **Broad:** "What's the full landscape of prerequisite data in Bruce BEM?"
2. **Narrow:** "For our prototype, where should we focus first?"

---

## Topic 4: Source

**Frame:**
> "What resources exist for understanding Bruce BEM's data structure? API docs? Database schema? UI where I can see it? And what's the best way for ME to learn this going forward?"

---

## Topic 5: Next

**Frame:**
> "What should we dive into first in our next session?"

---

## Notes

- **Meeting type:** Orientation + terminology alignment (first of potentially multiple sessions)
- **Expectation:** Ryan may not have all answers immediately - this is collaborative discovery
- **Follow-up:** Once we understand schema, we can articulate specific input data needs for Ali/Mujahid
