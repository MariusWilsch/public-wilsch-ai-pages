---
publish: true
---

# Hippocampus Auth: Platform Decision
[[hippocampus-auth]]

## Meeting Goal

All design decisions resolved via async review (issue comments, 2026-04-03). No meeting needed — proceeding to decomposition.

1. **Platform confirmed** — Cloudflare Pages + Access (two-phase: Jekyll now, VitePress later). Vercel and Obsidian Publish rejected.
2. **DNS resolved** — CNAME to `.pages.dev` from any DNS provider. No zone migration needed.

## Pre-Read

- [Design Doc: Hippocampus Auth Evaluation (Pass 2)](hippocampus-auth-evaluation) — updated evaluation with 4 options (current, Jekyll+CF, VitePress+CF, Vercel), comparison matrix, composability analysis, and agent automation assessment
- [Issue #1217](https://github.com/DaveX2001/deliverable-tracking/issues/1217) — Marius's two feedback comments (2026-04-02) that shaped this pass

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Platform selection: Cloudflare vs. Vercel vs. Obsidian Publish

✅ **Resolved (2026-04-03)** — Cloudflare Pages + Access confirmed. Two-phase path: Jekyll now, VitePress later.

- **Vercel rejected:** Auth cost ($20-170/mo), client friction (Vercel accounts required), dashboard-only user grants breaks agent automation
- **Obsidian Publish rejected:** No publishing API (GUI-only), no email OTP (static password only)
- **Cloudflare selected:** Free auth (email OTP, whitelist-only), 100% agent-automatable (CLI + API + official MCP server), per-path policies

### 2. DNS migration requirement

✅ **Resolved (2026-04-03)** — DNS migration is NOT required. The premise of this topic was incorrect.

- Cloudflare Pages supports custom domains via CNAME from any DNS provider on all plans (including free)
- Setup: `docs.wilsch-ai.com` → CNAME to `<project>.pages.dev` at current DNS provider
- No zone transfer needed. No DNS provider lock-in.
- Same pattern for new company instances: each domain adds its own CNAME

## Meeting Format

- **Status:** No meeting needed — all decisions made via async review (issue comments, 2026-04-03)
- **Outcome:** Design doc (Pass 3) is complete. Next step: decompose into implementation sub-issues.

## Related

- **Issue:** [#1217 — Evaluate Vercel for Hippocampus Publishing](https://github.com/DaveX2001/deliverable-tracking/issues/1217)
- **Design Doc:** [Hippocampus Auth Evaluation (Pass 3)](hippocampus-auth-evaluation)
- **Trigger:** [#847 — Contract Strategy](https://github.com/DaveX2001/deliverable-tracking/issues/847) (comments 35-36)
- **Related:** [#1349 — New Company Formation](https://github.com/DaveX2001/deliverable-tracking/issues/1349)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

