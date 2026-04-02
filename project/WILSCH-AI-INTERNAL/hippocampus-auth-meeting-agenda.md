---
publish: true
---

# Hippocampus Auth: Platform Decision
[[hippocampus-auth]]

## Meeting Goal

Finalize the hippocampus platform migration decision, now narrowed from four to two viable options after Pass 2 research. Options B (Netlify) and D (self-hosted) are eliminated.

1. **Platform confirmation** — Cloudflare or Vercel, given the auth, cost, and automation findings
2. **DNS migration** — whether moving `wilsch-ai.com` DNS to Cloudflare is acceptable (required for the CF path)

## Pre-Read

- [Design Doc: Hippocampus Auth Evaluation (Pass 2)](hippocampus-auth-evaluation) — updated evaluation with 4 options (current, Jekyll+CF, VitePress+CF, Vercel), comparison matrix, composability analysis, and agent automation assessment
- [Issue #1217](https://github.com/DaveX2001/deliverable-tracking/issues/1217) — Marius's two feedback comments (2026-04-02) that shaped this pass

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The auth cost asymmetry between Cloudflare and Vercel
⏱️ 10 min

Deep research reveals a stark difference in the auth model. Cloudflare provides email OTP for free with no client account requirement and full API automation. Vercel requires $20-170/month for production auth and either forces clients to create Vercel accounts or share a site-wide password.

- Cloudflare Access: free, email OTP, per-path policies, 100% agent-automatable
- Vercel: $20/mo minimum for production auth, clients need Vercel accounts, user grants are dashboard-only
- Vercel's advantage: better DX for VitePress, works with any DNS, faster setup (~30 min vs ~1 hour)
- The two-phase path (Jekyll+CF now → VitePress+CF later) avoids Vercel's auth cost while keeping the VitePress upgrade option

**To resolve:** Whether Vercel's DX and DNS flexibility justify the recurring cost and client friction, or whether Cloudflare's free auth and full automation make the platform choice clear.

### 2. DNS migration to Cloudflare as a prerequisite
⏱️ 10 min

Cloudflare Access requires DNS on Cloudflare — this is non-negotiable. If the team chooses the Cloudflare path, `wilsch-ai.com` DNS must move. The new company's domain would also need Cloudflare DNS.

- Current DNS location for `wilsch-ai.com` is unknown — migration scope depends on current provider
- Cloudflare DNS is free and widely used, but it means a single provider controls both hosting and DNS
- The new company (IMIF, #1349) will have its own domain — that domain also needs CF DNS if choosing the CF path
- Vercel has no DNS requirement — works with any provider via CNAME
- Moving platforms breaks all existing `mariuswilsch.github.io/...` links — redirect strategy needed

**To resolve:** Whether DNS migration to Cloudflare is acceptable for both `wilsch-ai.com` and the new company's future domain, and what happens to existing shared links.

## Meeting Format

- **Type:** Decision review
- **Duration:** 20 min (2 topics)
- **Expectation:** Read the updated design doc (Pass 2) before the meeting. The comparison table and Two-Phase Path section are the key decision inputs.
- **Outcome:** Platform confirmation + DNS decision. Implementation begins immediately after.

## Related

- **Issue:** [#1217 — Evaluate Vercel for Hippocampus Publishing](https://github.com/DaveX2001/deliverable-tracking/issues/1217)
- **Design Doc:** [Hippocampus Auth Evaluation (Pass 2)](hippocampus-auth-evaluation)
- **Trigger:** [#847 — Contract Strategy](https://github.com/DaveX2001/deliverable-tracking/issues/847) (comments 35-36)
- **Related:** [#1349 — New Company Formation](https://github.com/DaveX2001/deliverable-tracking/issues/1349)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

