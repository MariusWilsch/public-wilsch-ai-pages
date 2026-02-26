---
publish: true
---

# Step 1+2: Setup Phase — Hierarchy Understanding & Column Mapping
[[client-archibus]]

Design of the interactive Setup Phase for the AI Bulk Data Entry pipeline. Step 1 maps the client's data columns to BEM hierarchy levels. Step 2 maps the client's data columns to BEM schema fields. Together they produce the mapping contract that Step 3 consumes.

---

## Problem Statement

**Client data arrives in the client's language, not in BEM's language.**

Every client structures their facility data differently — different column names, different hierarchy depth, different conventions. Before Step 3 can process data level-by-level, two translations must happen: which columns represent hierarchy levels (Step 1), and which columns map to which schema fields (Step 2).

Today, the implementer performs both translations manually by filling out BEM's import Excel template. The AI takes over this translation; the implementer only confirms or corrects.

**Preconditions:**
- Client has provided a data file (Excel/CSV) with their facility data
- BEM asset schema is known and stable (35 fields, 9 hierarchy levels)
- Step 3 (fill level-by-level) is operational and waiting for the mapping contract

**Value proposition:** The implementer no longer fills out the import Excel template manually. The AI reads their data and produces the mapping — the implementer's time shifts from data entry to client communication.

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | AI produces a confirmed mapping contract from any client Excel — no manual template filling required |
| **Success** | Implementer uploads a data file, reviews AI's proposed mappings in conversation, and confirms with minimal corrections |
| **Done test** | Can Step 3 consume the mapping contract and process data without asking "which column is this?" → If YES → setup is complete |

---

## Approach

### Part 1: Hierarchy Inference (Step 1)

The implementer uploads a raw client Excel. The AI reads it and proposes which client columns map to which of the 9 BEM hierarchy levels.

**Inference mechanism:** Semantic analysis — not column name matching alone. The AI combines three signals:
1. **Column names** — semantic similarity to hierarchy level names
2. **Value content** — what the actual data values suggest (e.g., "CONCOURSE C" suggests a building-scale structure)
3. **Value cardinality** — few unique values = higher level, many = lower level

Column names alone are insufficient. Client data uses domain-specific naming (e.g., "AREA", "SUB AREA OF FACILITY", "LOCATION" in airport operations) that requires semantic reasoning to map to BEM levels like Building, Floor, Room.

**Authority model:** AI proposes with its best understanding, probes with useful questions when uncertain, but the implementer owns the lock-in decision. This is a conversational interaction — the AI presents, the implementer reacts, and the loop continues until the implementer explicitly confirms.

**Scope boundary:** AI maps only what exists in the client's data. It does not suggest adding hierarchy levels that the data doesn't contain. The value proposition is translating client data into BEM's language, not restructuring the client's data model.

**Rejection flow:** If the implementer disagrees, they provide context ("AREA means campus zones, not buildings") and the AI re-proposes. The correction loop is conversational — no re-upload, no UI reassignment. Loop runs until explicit confirmation.

**Output:** Confirmed mapping of client columns → BEM hierarchy levels. Step 3 uses this mapping to generate phantom location assets (Building, Floor, Room records that don't exist in the client's Excel but are required by BEM's parent-child structure).

#### Proposal Artifact

The AI presents its hierarchy proposal using progressive disclosure:

1. **Level names** (must-have) — which client columns map to which BEM levels
2. **Sample values + lightweight reasoning** (should-have) — evidence from the data, one line per level
3. **Expanded reasoning** (on-demand) — implementer can ask the AI to explain its inference

All levels appear in a single proposal. Uncertain levels are flagged inline — the AI does not split confident and uncertain levels into separate phases.

**Example — clean data (CAFM sample, 200 rows):**

| BEM Level | ← Client Column | Sample Values | Confidence |
|-----------|-----------------|---------------|------------|
| Building | Location | Building A, B, C, D | High |
| Floor | Floor | 1, 2, 3, G | High |
| Room | Room No. | 315, 330, 173 | High |

**Example — domain-specific data (Housekeeping Tracker, FMM Qatar):**

| BEM Level | ← Client Column | Sample Values | Confidence |
|-----------|-----------------|---------------|------------|
| Complex | AREA | CONCOURSE A, B, C | ⚠️ Medium |
| ??? | SUB AREA OF FACILITY | PTC, RETAILS | ⚠️ Uncertain |
| Room | LOCATION | DEPARTURE GATE C4 | High |

The clean data resolves in one confirmation. The domain-specific data triggers a correction loop — the AI surfaces the ambiguity with its reasoning, and the implementer provides the external context the data alone cannot supply.

#### Hierarchy Type Descriptions

BEM defines 9 hierarchy levels (Campus → Site → Complex → Property → Building → Floor → Stairwell → Suite → Room). The AI infers which levels apply by matching the client's data against **descriptions and examples per hierarchy AssetType** — not just the type name.

Without descriptions, the AI is guessing from a single word ("Complex" vs "Building"). With descriptions, the AI matches "4 concourse zones in a terminal" against a defined pattern.

**Proposed descriptions (starting point — to be validated with Rein):**

| BEM Type | Proposed Description | Examples |
|----------|---------------------|----------|
| Campus | Entire grounds spanning multiple buildings and sites | University campus, hospital campus |
| Site | A distinct land area within a campus | North wing, Terminal 1 |
| Complex | A group of related zones sharing infrastructure | Airport concourse, shopping mall wing |
| Property | A land parcel or named estate with its own address | Main Office Park |
| Building | A single enclosed structure | Office Tower A, Warehouse B |
| Floor | A horizontal level within a building | Floor 1, Ground Floor, Mezzanine |
| Stairwell | A vertical access shaft (sibling of Floor) | Stairwell A, Fire escape north |
| Suite | A named functional unit within a floor | Suite 204, Lab B, Retail zone |
| Room | An individual enclosed space | Room 315, Server Room, Gate C4 |

**Undefined:** Hierarchy type descriptions — BEM's asset_types file contains only structural codes (hierarchy depth), no descriptions or examples. Proposed descriptions above are AI-generated starting points. See [Meeting Agenda: Step 1+2 Hierarchy & Interpretation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-hierarchy-interpretation).

#### Name Enrichment

During hierarchy review, the AI also proposes a naming pattern. BEM stores all asset types in a single table — a floor named "3" is unreadable without its parent context. The AI compiles readable names by aggregating hierarchy context: "Building D Floor 3 Room 315" instead of just "315".

The enrichment rule is contextual: if the client's data already contains readable names (e.g., "CONCOURSE C"), the AI passes them through. If the data contains only codes or numbers (e.g., "3"), the AI enriches. The AI discriminates when to apply the rule — this is interpretation, not copying.

Readability validation is an AI concern — the AI flags unreadable names (e.g., "X1", "ABC123") during the interactive setup phase. The API accepts anything; the quality gate is in the AI layer.

**Undefined:** The exact naming enrichment logic and rule discrimination boundaries need empirical testing and stakeholder alignment. See [Meeting Agenda: Step 1+2 Hierarchy & Interpretation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-hierarchy-interpretation).

---

### Part 2: Column-to-Schema Mapping (Step 2)

With the hierarchy established, the AI maps remaining client columns to BEM's 35 schema fields.

**Mapping model:** Binary — every client column is either **mapped** (AI is confident) or **flagged** (AI cannot determine the match). Mapped columns go through batch confirmation. Flagged columns get individual 1x1 resolution with the implementer.

**Confirmation surface:** Chat-rendered table as the primary review artifact. The implementer sees the proposed mappings and confirms in conversation. Excel export is available as an option for deeper review.

**Training data:** The more diverse client Excels the team provides internally, the better the AI's context engineering becomes for column name recognition. Self-improving agent loops across deployments are a post-deployment architecture concern.

**Undefined:** Handling of unmappable columns (client columns with no BEM field match). See [Meeting Agenda: Unmappable Column Handling](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-unmappable-columns).

**Undefined:** Enum and value transformation — mapping client values to BEM lookup tables (e.g., equipment types, asset statuses, countries). Column mapping tells Step 3 WHERE data goes; value transformation tells it HOW to translate. See [Meeting Agenda: Enum & Value Transformation](rein-meeting-agenda-step2-schema-mapping).

---

### Part 3: Mapping Contract (Step 1+2 → Step 3 Handoff)

The combined output of Step 1 and Step 2 is the **mapping contract** — the artifact that Step 3 consumes to process data level-by-level.

**Role:** The mapping contract is the single source of truth for Step 3. Everything Step 3 needs to know about the client's data structure — which columns are hierarchy levels, which columns map to which BEM fields, which columns are excluded — lives in this contract. Step 3 never interprets client data directly; it reads through the contract.

**Visibility:** Internal JSON, invisible to the implementer. The AI internalizes BEM's schema and produces structured output from the raw client Excel. The implementer validates the mapping through the chat-rendered table (Part 2), not by inspecting the contract itself.

**Known contents:**
- Hierarchy level assignments (client columns → BEM levels)
- Schema field mappings (client columns → BEM fields)
- Flagged/excluded columns (with implementer's disposition)

**Undefined:** Exact contract schema — JSON structure, field names, how enum/value transformation rules are encoded, and how Step 3 consumes it. See [Meeting Agenda: Mapping Contract Schema](rein-meeting-agenda-step2-schema-mapping).

---

## Source

- **Design docs:**
  - [Chain 1B: Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design) (parent router)
  - [Step 3: Fill Level-by-Level](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
- **Transcripts:**
  - [AI Data Entry Demo (Feb 23, 2026)](/Users/verdant/Downloads/AI Data Entry Demo Transcript.txt)
- **Data artifacts:**
  - [AssetImportDescription (36 fields)](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit)
  - [Housekeeping Tracker (FMM)](/Users/verdant/Downloads/Housekeeping Tracker Jan 2026 v1.xlsb) — second Excel sample for hierarchy inference testing
- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852)
- **Project Index:** [#373](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/transcript-index-373)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/7d4d0137-c41c-4df3-835f-0218a90eba19.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/919879a0-f08e-4ce4-acfd-8ef68c39ef55.jsonl
