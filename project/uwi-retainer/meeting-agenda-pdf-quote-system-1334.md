---
publish: true
---

# Meeting Agenda: PDF Quote System — Open Design Questions (#1334)
[[client-02_uwi]]

## Meeting Goal

Three open design questions from the PDF quote system (#1334) need stakeholder input before implementation can begin.

1. **Hardware options for the configurator** — confirm which hardware beyond single DGX Spark is ready to offer
2. **Warenwirtschaft article mapping** — align the configurator's pricing model with Ulrich's billing system
3. **Tracking presentation** — decide how prospect usage data reaches Ulrich

## Pre-Read

- [Design Doc: PDF Quote System (#1334)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-pdf-quote-system-1334) — full context, especially Parts 2, 3, and 5 where the Undefined markers are
- [KI aus der Box Bundle Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/design-doc-ki-aus-der-box-bundle) — current product structure and pricing

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Hardware expansion beyond single DGX Spark
⏱️ 10 min

The configurator currently offers single DGX Spark only. Two expansion options exist in the product roadmap but aren't validated yet.

- Dual DGX Spark clustering (+€5K) is architecturally described in the bundle design doc — 256 GB unified memory, up to 397B parameter models
- Mac Studio M3 Ultra is listed on the ki-performance page as "Not measured" — no benchmark data exists yet
- Marius validated two-machine communication (1ms latency, 200 Gbps) but model sharding is still in validation

**To resolve:** Which hardware options beyond single DGX Spark are ready to include in the configurator, and what benchmark data is needed before each can be offered to customers.

### 2. Warenwirtschaft article structure for DGX Spark billing
⏱️ 10 min

The configurator generates a Richtpreisangebot, but the formal order flows through Wilsch KG Warenwirtschaft. Ulrich mentioned creating 4 articles for Busch to generate Auftragsbestätigungen — but the exact breakdown isn't specified yet.

- Working assumption: Hardware purchase, Rental, Setup Manntage, Betreuungsvertrag — mapped from the product's pricing layers
- The configurator's pricing model (purchase vs. rental) needs to align with how these articles are invoiced
- Thomas writes complex offers in Office, simple ones from Warenwirtschaft — the PDF quote system should reduce the Office path

**To resolve:** The exact article structure Ulrich will create in the Warenwirtschaft, and how the configurator's pricing options map to billable line items.

### 3. Tracking presentation for Ulrich
⏱️ 5 min

Per-account usage tracking is being built — pages visited, time spent, frequency. The open question is how this data reaches Ulrich so he can use it for sales follow-up.

- Marius mentioned heatmap-style visualization as an aspiration
- Current plan: manual lookup by the team when Ulrich asks
- Options range from simple (email digest) to sophisticated (admin dashboard with per-prospect activity timelines)
- The right level depends on how frequently Ulrich checks and what decisions the data drives

**To resolve:** The minimum viable presentation that gives Ulrich actionable insight into which prospects are engaged and which are cold.

## Meeting Format

- **Type:** Review / decision
- **Expectation:** Marius to confirm hardware readiness status; Ulrich to confirm Warenwirtschaft article plan
- **Outcome:** Three design questions resolved → Undefined markers removed from design doc → implementation can begin

## Related

- **Issue:** [#1334](https://github.com/DaveX2001/deliverable-tracking/issues/1334) — AI-in-a-Box PDF Quote Generation System
- **Design Doc:** [PDF Quote System](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-pdf-quote-system-1334)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

