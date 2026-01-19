---
publish: true
---

# Chain 1B: AI Bulk Data Entry - Alignment Document
[[client-archibus]]

## Problem

Partners spend days reformatting client Excel for Bruce onboarding. This is low-margin, repetitive work that holds up implementations. Data must follow strict hierarchy order (site → building → floor → room) due to foreign key dependencies.

## Success Definition

> **AI fills schema where confident, asks where uncertain, flags what it cannot resolve - using schema validation as the feedback loop for self-correction.**

Think of it like spell-check for data structure: red squiggles appear when data doesn't fit the schema, with suggestions for how to fix it.

**Goal:** Speed up the process, not achieve 100% perfection. If we can reduce partner onboarding time, that's success.

## How It Works

```
INPUT (messy Excel) → MAPPING → SCHEMA (feedback) → OUTPUT (accepted + rejected)
                                    ↑
                              AI iterates here
```

1. **Client uploads messy Excel** - inconsistent columns, varying naming conventions
2. **AI maps columns to Bruce schema fields** - fuzzy matching, semantic understanding
3. **Schema accepts or rejects** - with actionable reason for each rejection

When AI is uncertain about a mapping, it asks a clarifying question rather than guessing wrong.

## What the Demo Shows

End-to-end flow demonstrating:

1. **Input:** Messy client Excel (like what Mujahid showed from Qatar Museum)
2. **Processing:** AI validates against Bruce schema, asks clarifying questions
3. **Output:** Clean data ready for import + actionable rejection list for human decisions

The wow factor: "Here's messy data. Watch AI make it Bruce-ready."

---

*Synthesis of Dec 29 + Dec 30 meetings. For alignment - does this match your understanding?*

*Refs: [#373](https://github.com/DaveX2001/deliverable-tracking/issues/373)*
