---
publish: true
---

# Meeting Agenda: PDF Quote System — Open Design Questions (#1334)
[[client-02_uwi]]

## Meeting Goal

One open design question from the PDF quote system (#1334) needs Marius's input — hardware options for the configurator.

1. **Dual DGX Spark model sharding** — confirm whether clustering is customer-ready or still in validation

*Previously 3 questions (pass 1), reduced to 1 (pass 2), expanded to 2 (pass 3), reduced to 1 (pass 4). Auth isolation resolved — Cloudflare Access Gateway replaces Supabase auth entirely (pass 4). Value management resolved — Payload CMS on Cloudflare (pass 4). Warenwirtschaft resolved (pass 2). Tracking tool selection confirmed — PostHog (pass 3/4).*

## Pre-Read

- [Design Doc: PDF Quote System (#1334)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-pdf-quote-system-1334) — full context, especially Part 3 where the remaining Undefined marker is
- [KI aus der Box Bundle Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/design-doc-ki-aus-der-box-bundle) — current product structure and pricing
- [Hardware-Strategie: Lokale KI-Inferenz](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/hardware-strategie-lokale-ki-inferenz) — DGX Spark clustering specs, Phase 1/2

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Dual DGX Spark model sharding customer-readiness
⏱️ 10 min

The Bundle design doc describes Dual DGX Spark clustering as confirmed — Qwen3.5-397B, launch-cluster.sh, +€5K. The Hardware-Strategie confirms two units are operational with vLLM. But implementation experience suggests model sharding may not yet be reliable enough for customer-facing use.

- Two-machine communication validated: 1ms latency, 200 Gbps via ConnectX-7
- Qwen3.5-397B listed as "confirmed" in Bundle design doc (March 25)
- Model sharding listed as "still in validation" in PDF Quote design doc (April 2)
- Mac Studio M3 Ultra is out of scope — different architecture (disaggregated inference, not clustering)

**To resolve:** Whether Dual DGX Spark model sharding is reliable enough to offer customers in the configurator, or whether it stays as a future addition after further validation.

## Meeting Format

- **Type:** Review / decision
- **Expectation:** Marius to confirm model sharding status (Topic 1)
- **Outcome:** Last Undefined marker resolved → design doc complete → decomposition for Developer can begin

## Related

- **Issue:** [#1334](https://github.com/DaveX2001/deliverable-tracking/issues/1334) — AI-in-a-Box PDF Quote Generation System
- **Design Doc:** [PDF Quote System](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-pdf-quote-system-1334)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

