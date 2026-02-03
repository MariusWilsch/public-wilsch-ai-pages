---
publish: true
---

# Meeting Agenda: Step 2 - Schema Mapping
[[client-archibus]]

## Meeting Goal

By the end of this session, we should have:

1. **Cell value clarity** — For each column: data type, enum values if applicable (JSON schema)
2. **Input format clarity** — One sheet expected, or handle multiple?
3. **API vs AI field split** — Which fields does AI map vs API fills?
4. **Design validation** — Does the column-level mapping approach make sense?

## Pre-Read

Please review the design document before the meeting:

[Step 2: Schema Mapping - Design Document](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-2-schema-mapping-design)

---

## Discussion Topics

### 1. Cell Value Constraints

For each Bruce BEM field:
- What's the data type? (string, enum, guid, date)
- If enum: what are the allowed values?
- Can we see a Bruce BEM export to compare against client input?

### 2. Input Format Expectations

- Should we expect one Excel with one sheet?
- What if client provides multiple sheets or files?
- Where are headers typically located?

### 3. API-Filled vs AI-Filled Fields

- Which of the 35 asset fields does the API fill automatically?
- Which fields should AI map from client data?

### 4. Column Mapping Design Review

Walk through the design doc approach:
- AI proposes semantic mapping
- High confidence = batch confirm
- Uncertain = one-by-one review
- Does this make sense?

---

## Meeting Format

- **Type:** Technical alignment session
- **Attendees:** Rein, Marius
- **Expectation:** Review design doc beforehand; bring schema knowledge
- **Outcome:** Clarity on TBDs to proceed with implementation

---

## Related

- **Issue:** [#373 - AI Bulk Data Entry Prototype](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Parent Design:** [Chain 1B: AI Bulk Data Entry](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
- **Schema Reference:** [Asset Import Schema (SharePoint)](https://aschs-my.sharepoint.com/:x:/g/personal/rein_suurvali_asc-hs_com/IQCZWnERRmHxRb3r4bb3BZzRAfjwreav4FEP85btLlaVWNI)
