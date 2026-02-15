---
publish: true
---

# Meeting Agenda: Step 3 — Field Review & Undefined Items
[[client-archibus]]

## Meeting Goal

Validate field-level decisions from the Feb 14 extraction pass. The design doc's field tiers are now defined, but 6 fields remain Undefined and 12+ field descriptions need confirmation.

1. **Resolve 6 Undefined fields** — clarify purpose, data source, or remove from import scope
2. **Validate proposed field descriptions** — 12+ fields in the [Google Sheet](https://docs.google.com/spreadsheets/d/10h8P4dMlLDpkHikUK6enDFeYtRoCwMVm/edit) have no/inadequate comments. Proposed text is highlighted for review.
3. **Confirm AssetType enum update** — new 23 facility types replace old category, one potential typo

## Pre-Read

- [AssetImportDescription — Fields Assets sheet](https://docs.google.com/spreadsheets/d/10h8P4dMlLDpkHikUK6enDFeYtRoCwMVm/edit) — highlighted cells have proposed comments for review
- [Chain 1B: Step 3 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design) — updated Field Tiers section
- [Asset Types (old)](https://docs.google.com/spreadsheets/d/1Wc1BL18e5Vaxx7bAzsvxAeWojfvxRrWd/edit) — compare with new 23 facility types below

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Most field descriptions are empty — the AI needs context to map correctly

The Fields Assets sheet has 36 import fields. 12+ have no comment at all. The AI will use these comments as instructions during Step 2 mapping. Without them, the AI relies on field names alone, which leads to ambiguous mappings (e.g., "DateActiveService" could mean installation date, commission date, or operational start date).

Proposed comments are highlighted in the [Fields Assets sheet](https://docs.google.com/spreadsheets/d/10h8P4dMlLDpkHikUK6enDFeYtRoCwMVm/edit) for the following fields: DateManufacture, DatePurchased, DateActiveService, DateNextReplacement, WarrantyFrom, WarrantyTo, AreaInteriorM2, AreaTotalM2, AreaRentableM2, MainContactName, MainContactPhone, MainContactEmail, Name, Id, ParentId.

**We need confirmation that each proposed description matches what Bruce expects for that field, or corrections where they don't.**

### 2. Three fields exist in the schema but their purpose and data source are unclear

**AddressExplanation** (row 13): Rein's own comment says "Review if is in use, and what is corresponding DB field?" Example: "Follow the signs to the building when approaching from south."

**LocationDescription** (row 18): "Additional Description of location, like on top of the hill." Overlaps with Address?

**ClearanceInstructions** (row 12, renamed from SecurityClearanceStatus): Example is "Access by access card. Card at Admin desk safe." Reads like location-level, not equipment-level.

**We need a decision per field: keep in AI scope (with a clear description of what data goes here), or remove from the import template for PoC.**

### 3. Address is defined as a 50-character string — unclear what exactly goes in it

City, State, PostalCode, and Country each have their own fields. That leaves Address as likely just the street + house number (e.g., "Kotzebue tn 18a"). But the Excel comment just says "Asset address, if supplied" — too vague for consistent AI mapping.

Similarly, City and State are typed as free-form strings. If a client writes "Tallinn" vs "TALLINN" vs "tallinn" — does Bruce care? Is there an ENUM for cities/states, or is it truly free-form?

**We need: (a) confirmation that Address = street + house number only, (b) whether house number is required or just street is OK, and (c) whether City/State validate against a list or accept any text.**

### 4. Barcode and QrCode lack real-world examples and have unclear scope

SerialNumber is straightforward (e.g., "45698712365"). But Barcode's example is a Gmail URL (placeholder, not real). QrCode's comment is just "Make it longer."

The SerialNumber comment adds: "If the barcode reader is in use, only numbers" — does this mean barcode scans go to SerialNumber, or to the Barcode field? The relationship between these two is unclear.

**We need: (a) a real-world Barcode value, (b) a real-world QrCode value, (c) clarification of when a scanned barcode goes to SerialNumber vs the Barcode field, and (d) confirmation that QrCode needs a DB length extension beyond 50 characters.**

### 5. Two status ENUM fields exist in the import template with no visible distinction

Row 8 (`AssetStatus`, String FK, 50 chars) says "ENUM Choice from predefined statuses" — this maps to the 26-value asset-status-enum (Active, Broken, Decommissioned, In Repair, etc.).

Row 24 (`Status`, String, 100 chars) also says "ENUM, must match to list of statuses" — but has no further description, no example, and no separate ENUM list.

Both fields reference "statuses" but no second list exists in the artifacts. The CAFM sample data has a "Status" column (Active/Inactive) and a "Condition" column (Good/Fair/Poor) — it's unclear which maps where.

**We need: clarification of what Status (row 24) is for. Is it the same ENUM as AssetStatus? A simplified active/inactive flag? Or something else entirely? If redundant, can it be removed from the import scope?**

### 6. The updated AssetType facility list has one potential naming issue

The new 23 Facility-Related types replace the old category. The full list was provided via chat (Feb 14): Cooling, Heating, Amenity, Decoration, Emergency, Equipment, Fire Safety, Furnishing, Good, HVAC, Lighting, Spare Part, Appliances, SHERQ equipment, Signing, Surveillance, Vertical Transport, Electrical, Other Equipment, Plumbing, Waste Water, Automation, Safety.

One entry reads "Signing" (Labels, Screens) — the old list had "Signage."

**We need: (a) confirmation that "Signing" is intentional or should be "Signage", and (b) confirmation that this list fully replaces the old Facility-Related category while the other three categories (Employee Personal, Information Management, Location-Related) remain unchanged.**

### 7. The API error response needs to include the failing node's Id for self-correction

The current error format (agreed Feb 11) returns field name, value, and reason — but not WHICH node in the hierarchy failed. With the generic `children` structure, a building JSON may have 50+ nodes. Without an Id reference, the AI cannot locate the failing node to fix it.

The AI holds the full JSON tree in memory and can self-construct human-readable paths (Property > Building A > Floor 3 > Room 194) from the Id + ParentId chain. The backend only needs to return the failing node's temp Id.

**We need: confirmation that the error response will include the failing node's Id field, so the AI can find, fix, and resubmit without manual intervention.**

### 8. Low-priority items — acknowledged, not blocking

**Country enum cleanup:** 39 of 249 country entries have parenthetical suffixes (e.g., "Korea (Republic of)"). Fuzzy matching works around this, but a clean list would be simpler. Blocked on Miguel.

**GUID mapping after import:** Does the API return a mapping of temp IDs → real GUIDs after successful insert? Not needed for PoC (single building, no follow-up operations), but may matter for multi-building imports where Building B references the same Property as Building A.

**These don't need discussion time unless Rein has quick answers.**

## Meeting Format

- **Type:** Field-level review
- **Expectation:** Rein reviews the highlighted cells in the [Fields Assets sheet](https://docs.google.com/spreadsheets/d/10h8P4dMlLDpkHikUK6enDFeYtRoCwMVm/edit) before the meeting
- **Outcome:** Undefined fields resolved (keep/remove), proposed comments validated, AssetType enum confirmed → design doc and sheet updated

## Related

- **Issue:** [#373 — ARCHIBUS: AI Bulk Data Entry](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Design Doc:** [Chain 1B: Step 3 Fill Level-by-Level](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
- **Fields Assets Sheet:** [AssetImportDescription (Google Drive)](https://docs.google.com/spreadsheets/d/10h8P4dMlLDpkHikUK6enDFeYtRoCwMVm/edit)
