---
publish: true
---

# Meeting Agenda: Bruce BEM Data Structure for AI Bulk Entry
[[client-archibus]]

## Meeting Goal

Establish shared understanding of Bruce BEM's data structure to support the AI bulk data entry prototype. By the end of this session, we should be aligned on:

1. **Terminology** - What Bruce BEM calls "background data" or prerequisite data
2. **Structure** - How the data hierarchy works in Bruce BEM
3. **Next steps** - What to explore in follow-up sessions

## Pre-Read

Please review this design document before the meeting:

[Chain 1B: AI Bulk Data Entry - Design Document](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)

---

## Discussion Topics

### 1. Project Context

We're building an AI-assisted bulk data entry system to reduce partner time spent on data formatting during onboarding. The AI would fill data where confident, ask where uncertain, and flag what needs human decision.

To make this work, we need to understand what prerequisite data must exist in Bruce BEM before asset data can be loaded.

### 2. Terminology Alignment

Ian described "background data" as three buckets with foreign key dependencies:

| Bucket | Example Hierarchy |
|--------|-------------------|
| Spatial | Site → Building → Floor → Room → Desk |
| Assets | Similar hierarchical patterns |
| People | Department → Division → hierarchy |

**Discussion:** How does Bruce BEM structure this? What terminology does Bruce use for these concepts?

### 3. Scope

**Two questions:**
1. What's the full landscape of prerequisite data in Bruce BEM?
2. For the prototype, where should we focus first?

### 4. Resources

What documentation or tools exist for understanding Bruce BEM's data structure?
- API documentation?
- Database schema?
- UI where we can see the hierarchy?

### 5. Next Steps

What should we dive deeper into in a follow-up session?

---

## Meeting Format

- **Type:** Orientation and terminology alignment
- **Expectation:** This is the first of potentially multiple sessions - collaborative discovery, not a deep technical dive
