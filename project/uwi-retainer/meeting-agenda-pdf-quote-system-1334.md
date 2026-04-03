---
publish: true
---

# Meeting Agenda: PDF Quote System — Open Design Questions (#1334)
[[client-02_uwi]]

## Meeting Goal

Two open design questions from the PDF quote system (#1334) need Marius's input — one on hardware options, one on prospect account security.

1. **Dual DGX Spark model sharding** — confirm whether clustering is customer-ready or still in validation
2. **Auth isolation strategy** — choose between extending Supabase RLS or adding Cloudflare Access for prospect accounts

*Previously 3 questions, reduced to 1 (pass 2), now 2 (pass 3). Warenwirtschaft resolved (pass 2). Tracking tool selection resolved — PostHog recommended (pass 3). Value management documented with two options — composable decision, no blocker (pass 3).*

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

### 2. Auth isolation strategy for prospect accounts
⏱️ 10 min

The PDF quote system creates prospect accounts on the same Supabase project (WILSCH_AI_PROJECTS-PROD) that serves Invoice Agent, Call2Tanss, and a tax module. Invoice Agent's sensitive tables are protected by AAL2/MFA — prospects with basic email+password accounts (AAL1) cannot access invoices or tax keys. But cost_centers, call profiles, and tax tables have no such gate.

- Four applications share one auth pool: public, invoice_agent, call_your_stars, tax
- Invoice Agent: AAL2 gate on invoices_detail, invoices_header, tax_keys, transfer_audit_log
- Exposed: invoice_agent.cost_centers (any authenticated), call_your_stars.profiles (any authenticated), tax schema (no RLS)
- Marius flagged this in his April 3 review — "dieses Cloud Fair eigentlich voll crazy lazy"

**To resolve:** Whether to extend the existing AAL2/RLS pattern (add policies to exposed tables, prospects stay AAL1) or add Cloudflare Access as a per-application gateway — including how much operational overhead is acceptable.

## Meeting Format

- **Type:** Review / decision
- **Expectation:** Marius to confirm model sharding status (Topic 1) and choose auth isolation approach (Topic 2)
- **Outcome:** Both Undefined markers resolved → design doc complete → implementation can begin

## Related

- **Issue:** [#1334](https://github.com/DaveX2001/deliverable-tracking/issues/1334) — AI-in-a-Box PDF Quote Generation System
- **Design Doc:** [PDF Quote System](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-pdf-quote-system-1334)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

