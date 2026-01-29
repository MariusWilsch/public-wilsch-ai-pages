---
publish: true
---

# AI Work Track Prioritization - Meeting Agenda
[[client-archibus]]

**Attendees:** All-hands (full team)

---

## Meeting Goal

**Core question:** For each AI work track, do we kill, pivot, or continue?

If continue, we should also align on:
1. Resource assignment (who works on what)
2. Timeline commitments (when)
3. Next steps per track

---

## Pre-Read

Please review these documents before the meeting:

- [Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
- [Auth Approaches](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/auth-approaches-comparison)
- [Asset Code Generator Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/asset-code-generator-design)
- [NoVNC Hybrid Environment](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/adr-novnc-docker-hybrid-environment)

---

## Discussion Topics

### 1. AI Bulk Data Entry (#373)

**Stage:** Design progressing (core work)

3-step process: understand hierarchy → map input schema → fill level-by-level. Design doc updated with concrete schema from Rein.

**Discussion:** Crystallize next steps:
- Step 1 (Hierarchy): Who should I talk to about the real-world process?
- Step 2 (Mapping): Who provides input data + validates AI's work?
- Step 3 (Fill): When can the insertion API be built?

### 2. Asset Code Generation (#605)

**Stage:** Early design (idea validation)

AI generates equipment codes from client data (Building-Floor-Room-Type-Sequence pattern). Discussed with Mujahid, needs buy-in from Miguel.

**Discussion:** Kill / Pivot / Continue? Do we want to offer this as a product?

### 3. Standalone Authentication

**Stage:** Iframe works, standalone blocked

Current iframe approach works (Bruce BEM passes tokens). Standalone OAuth blocked by Code on Time framework. Approach C (Tiny Auth App) proposed as workaround.

**Discussion:** Continue with Approach C / Stay with iframe for now / Kill standalone effort?

### 4. NoVNC Hybrid Environment (#628)

**Stage:** Prototype ready (8/8 ACs passed, deployable)

AI navigates Chrome visually so users see actions happen. Split-screen wrapper combines chat + VNC view.

**Discussion:** Kill / Pivot / Continue?

---

## Decisions Needed

- [ ] Kill/Pivot/Continue verdict for each track
- [ ] Resource assignment (who works on what)
- [ ] Timeline commitments (when)

---

## Meeting Format

- **Type:** Prioritization / decision meeting
- **Expectation:** Come having read the pre-read docs. We present status, discuss, then decide.
