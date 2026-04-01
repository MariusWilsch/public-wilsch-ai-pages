---
publish: true
---

# AI Bulk Data Entry: From Messy Excel to Clean Import

[[client-01_archibus]]

Streamline client onboarding with automated bulk data entry for BruceBEM —
turning data reformatting from high-effort, low-margin work into an
automated process.

---

## The Problem You Already Know

Every new client onboarding starts the same way. A partner receives an Excel
file — sometimes clean, usually not — containing thousands of assets that need
to enter Bruce BEM. Buildings, floors, rooms, equipment. Each column named
differently. Each client's hierarchy structured their own way.

Someone on the team opens the spreadsheet and starts mapping. Column by column.
Row by row. Figuring out which fields match the BEM schema, which values need
translating, which rows don't belong. It takes hours. Sometimes days. And when
something goes wrong — a wrong enum, a missing parent location, a field that
doesn't validate — it means re-doing the work.

This is high-effort, low-margin work. The kind of work that keeps experienced
implementers tied to spreadsheets instead of doing what they're good at:
configuring systems and solving client problems.

**And every new client brings another spreadsheet.**

---

## What Your Partners Spend Their Time On

The frustration isn't the complexity — it's the repetition:

- _"Every client sends a different format. We start from scratch every time."_
- _"I know the BEM schema by heart, but matching 30 columns manually still takes a full day."_
- _"When one field fails validation, we re-import the entire batch. Then we find the next error."_

These aren't edge cases. This is the default workflow for every background data
import. The same 35 schema fields. The same hierarchy levels. The same
validation rules. Only the column names change — and that's exactly the part
an AI can handle.

---

## Proven on Real Data

A real CAFM asset spreadsheet — the kind partners receive from clients
every week — was fed to the pipeline.

The AI read the column headers, inferred the building hierarchy from the data,
and mapped every field to the BEM schema. Seven asset types. Two status values.
Location hierarchy four levels deep. It built the import JSON, submitted it to
Bruce BEM's validation endpoint, received structured errors — and fixed them
itself.

No manual intervention. No re-mapping. No second attempt by a person.

_The imported data was confirmed correct in BruceBEM by an implementation partner._

---

## How It Works

1. **The AI understands the hierarchy** — it reads the spreadsheet, detects
   which columns represent locations (campus, building, floor, room), and
   proposes the hierarchy structure. The implementer confirms with one click.

2. **The AI maps columns to the BEM schema** — 35 fields, each matched
   semantically. Asset types, status values, and enums are resolved
   automatically against BruceBEM's reference data. The implementer reviews
   the mapping contract.

3. **The AI imports and self-corrects** — it builds the nested JSON, submits
   to BruceBEM's API, and when validation errors come back, it reads the
   structured error response and fixes the data. No human in the loop for
   error correction.

Upload. Confirm. Import. That's the partner's workflow.

---

## What Changes

The spreadsheet that used to take a day takes minutes. The validation errors
that used to mean rework get resolved automatically. The mapping that used to
require a BEM expert now happens through AI inference.

Partners focus on what matters — system configuration, client relationships,
solution design — while the pipeline handles the data grunt work.

Every new client onboarding becomes the same simple process: upload the
spreadsheet, confirm the AI's mapping, and import. The format doesn't matter.
The column names don't matter. The hierarchy depth doesn't matter. The AI
adapts.

**The spreadsheets stop being a bottleneck. The onboarding accelerates.**

---

## See It in Action

Bring a client spreadsheet — any format, any column naming, any hierarchy
depth — and see the pipeline process it live.

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

