---
publish: true
---

# Design Doc: Power10 Memory Compatibility — EMRF/EM7F vs EM78 for 9105-42A
[[client-02_uwi]]

Compatibility matrix documenting whether L1024 memory feature codes (EM7F, EMRF) can replace S1024 feature code EM78 on the IBM Power10 9105-42A — driven by a cost-saving procurement opportunity.

---

## Problem Statement

The IBM Power10 S1024 (9105-42A) requires feature code EM78 for its 256 GB DDR4 DDIMMs — at $60,000 per pair. A client in Ulrich's network has EM7F-labeled units available at lower cost. EM7F is officially designated for the Power L1024 (9786-42H), not the S1024. The question: is this a hard technical restriction or just an IBM ordering label on the same physical hardware?

A parallel question exists for EMRF — another L1024 feature code for the same DIMM size. If both use identical hardware internally, the procurement constraint is commercial, not technical.

**Preconditions:**
- Target system is a Power10 S1024 (9105-42A) with 4U DDIMM slots
- EM78 is the only 256 GB DDR4 feature code IBM lists for 9105-42A (Table 1, IBM plugging rules)
- EM7F and EMRF are listed for 9786-42H only (Table 3, IBM plugging rules)
- Ulrich's client can supply EM7F units at a better price point

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Ulrich can confidently procure EM7F (or EMRF) units for a 9105-42A system — knowing the technical compatibility, warranty implications, and cost trade-off |
| **Success** | Ulrich reads this document and can answer his client's question definitively: "yes, these DIMMs work in the S1024" or "no, here's why not" — without needing to call IBM |
| **Done test** | Does the compatibility matrix answer both key questions? (1) Can EM7F/EMRF physically replace EM78 on 9105-42A? (2) What are the CCIN and part numbers for EMRF? |

---

## Approach

### Part 1: The "Technical Number" — CCIN 32AE

Every IBM DDIMM carries a CCIN (Custom Card Identification Number) — the hardware identifier that the system reads on boot. Feature codes like EM78 or EM7F are ordering labels, not hardware identifiers. The system sees the CCIN, not the feature code.

All three feature codes in question resolve to the same physical module:

| Identifier | Value |
|-----------|-------|
| **CCIN** | 32AE |
| **Part Number (FRU)** | 01GY925 |
| **Capacity per DIMM** | 256 GB |
| **Type** | DDR4 |
| **Speed** | 2933 MHz |
| **Density** | 16 Gbit |
| **Height** | 4U |

Midland Information Systems lists EM78 and EM7F as a single product under one URL — same CCIN, same part number. DataTech lists the same 01GY925 / 32AE for EM78 on the E1050 (9043-MRX). Midland also lists EMC4 (the E1080 variant) with the same 32AE / 01GY925 in a 4-DIMM configuration.

This is not an anomaly. IBM uses different feature codes for identical hardware across system models — the same pattern applies to PCIe adapters (EN2L/EN2M share CCIN 2CFC, EJ2B/EJ2C share CCIN 57F2).

### Part 2: Feature Code Breakdown — EM78 vs EM7F vs EMRF

The three feature codes differ only in their IBM ordering context — target system and bundle size:

| | EM78 | EM7F | EMRF |
|---|---|---|---|
| **Target System** | S1024 (9105-42A) | L1024 (9786-42H) | L1024 (9786-42H) |
| **DIMMs per Feature** | 2 (standard pair) | 2 (standard pair) | 4 (SAP bundle) |
| **Total per Feature** | 512 GB | 512 GB | 1024 GB |
| **Per-DIMM Capacity** | 256 GB | 256 GB | 256 GB |
| **Height** | 4U | 4U | 4U |
| **CCIN** | 32AE | 32AE | 32AE |
| **Part # (FRU)** | 01GY925 | 01GY925 | 01GY925 |
| **Ordering Rules** | Standard | Standard | SAP program, min 2 TB |

EMRF is part of IBM's SAP memory bundle line (EMRX for 64 GB, EMRV for 128 GB, EMRF for 256 GB). These bundles carry different ordering constraints — minimum 2 TB total, L1024 only — but the individual DIMMs inside are physically identical to the standard EM7F and EM78 DIMMs.

The S1024 and L1024 are both 4U chassis, so there is no form factor incompatibility between their DDIMMs.

### Part 3: Compatibility Verdict

**Q1: Can EM7F or EMRF physically/electrically replace EM78 on 9105-42A?**

**Yes.** The individual DIMMs are identical hardware — same CCIN (32AE), same FRU part number (01GY925), same DDR4 256 GB 2933 MHz spec, same 4U form factor. The S1024 identifies memory by CCIN on boot, not by the feature code printed on the purchase order. No firmware-level feature code validation was found in IBM documentation or community forums.

Supporting evidence:
- Midland lists EM78 + EM7F under one product listing (shared CCIN)
- IBM reuses the same CCIN/FRU across 4+ feature codes (EM78, EM7F, EMC4, EMRF) spanning 4 different system models
- DDR4 DDIMMs from Power10 systems are forward-compatible with Power11 via the same OMI interface — confirming cross-system hardware identity

**Q2: What are CCIN and part number for EMRF?**

Each individual DIMM in an EMRF bundle carries **CCIN 32AE** and **Part # 01GY925** — identical to EM78 and EM7F. EMRF is a 4-DIMM SAP ordering bundle, not a distinct piece of hardware. It has no unique CCIN of its own.

**Warranty note:** Since the CCIN and FRU are identical, IBM support tools identify the DIMM the same way regardless of which feature code was on the original purchase order. A support technician running diagnostics on a 9105-42A would see "CCIN 32AE / 01GY925" — indistinguishable from an EM78-purchased unit.

---

## Source

- **IBM Documentation:** [Plugging rules for memory modules — 9105-41B, 9105-42A, 9786-42H](https://www.ibm.com/docs/en/power10/9105-41B?topic=modules-plugging-rules-memory-in-system) (Tables 1 and 3)
- **Vendor — DataTech:** [EM78 512GB DDR4 Listing](https://datatechcomputer.com/memory/ibm-em78-512gb-Power10-S1024-9105-42A-ddr4-memory/) (CCIN 32AE, Part# 01GY925)
- **Vendor — Midland:** [32AE EM78/EM7F 512GB Listing](https://www.midlandinfosys.com/ibm-32ae-01gy925-em78-em7f-512gb-ddr4-memory-2x256gb-ddimms) (shared product page)
- **Vendor — Midland:** [32AE EMC4 1TB Listing](https://midlandinfosys.com/ibm-32ae-emc4-01gy925-1tb-ddr4-memory-9080-hex-e1080) (same CCIN on E1080)
- **IBM Announcement:** [Enhanced Power10 capabilities (ENUS123-017)](https://www.ibm.com/common/ssi/cgi-bin/ssialias?infotype=an&subtype=ca&appname=gpateam&supplier=897&letternum=ENUS123-017) (EMRF/EMRV SAP bundle specs)
- **Cross-gen compatibility:** [IT Jungle — IBM Preserves Memory Investments](https://www.itjungle.com/2025/05/21/ibm-preserves-memory-investments-across-power10-and-power11/)
- **Session:** `5cabfe4e-32ae-4652-9811-458fbd5c211d`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

