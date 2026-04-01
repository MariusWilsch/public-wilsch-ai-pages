---
publish: true
---

# Hippocampus Auth: Platform Decision
[[hippocampus-auth]]

## Meeting Goal

Decide which publishing platform to migrate hippocampus to, given four evaluated options with trade-offs in auth model, effort, cost, and vendor dependency.

1. **Platform decision** — select Option A, B, C, or D (or a hybrid) based on the evaluation
2. **Domain strategy** — decide on the public-facing URL for the authenticated site
3. **SSG question** — keep Jekyll now or invest in modernization (VitePress)

## Pre-Read

- [Design Doc: Hippocampus Auth Evaluation](hippocampus-auth-evaluation) — full evaluation with 4 options, comparison matrix, and trade-offs
- [Issue #1217](https://github.com/DaveX2001/deliverable-tracking/issues/1217) — original issue with Marius's comment on requirements

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The client sharing friction trade-off
⏱️ 10 min

Each option handles client sharing differently. Options A and C ask clients for email OTP (30 seconds of friction). Option B requires clients to create an account. Option D generates signed URLs with zero client friction but requires Marius to run a script.

- Currently Marius sends a link and the client clicks — zero friction, zero auth
- Email OTP is the lightest auth (no account, no password) but still 30 seconds
- Signed URLs are invisible to the client but need a generation step on Marius's side
- The decision depends on how often links are shared and how tech-savvy clients are

**To resolve:** The acceptable friction level for client document sharing — whether the client experience or the admin experience matters more.

### 2. Vendor dependency vs operational ownership
⏱️ 10 min

Options A–C trade operational burden for vendor dependency. Option D trades vendor dependency for operational ownership. Both are valid — the question is which risk the team prefers to carry.

- Cloudflare/Netlify handle uptime, TLS, CDN, builds — but the auth model is their product decision
- Self-hosted means Caddy + Docker on existing servers — full control, but the team owns incidents
- The team already runs Docker services on wilsch-ai and DGX Spark, so ops capability exists
- If Cloudflare changes pricing or discontinues the free Access tier, migration happens under pressure

**To resolve:** Whether the team's existing operational capability makes self-hosting viable, or whether zero-ops managed platforms are the better investment of attention.

### 3. The domain question shapes the migration
⏱️ 5 min

The current URL is `mariuswilsch.github.io/public-wilsch-ai-pages/`. Moving to any alternative changes this. The new domain choice affects DNS requirements, branding, and which options are feasible.

- Options A and C require DNS on Cloudflare — if the domain is already on Cloudflare, zero friction; if not, DNS migration is a prerequisite
- Option D uses any DNS — point a subdomain to the VPS
- `docs.wilsch-ai.com` is the natural candidate, but any subdomain works
- Existing external links to the `github.io` URL will break regardless of which option is chosen — a redirect from the old URL is possible if GitHub Pages stays active during transition

**To resolve:** The target domain for the authenticated hippocampus site, and whether existing `github.io` links need redirect handling.

## Meeting Format

- **Type:** Decision review
- **Duration:** 25 min (3 topics)
- **Expectation:** Read the design doc evaluation before the meeting. Come with a leaning toward one of the four options.
- **Outcome:** Selected option + domain decision. Implementation can begin immediately after.

## Related

- **Issue:** [#1217 — Evaluate Vercel for Hippocampus Publishing](https://github.com/DaveX2001/deliverable-tracking/issues/1217)
- **Design Doc:** [Hippocampus Auth Evaluation](hippocampus-auth-evaluation)
- **Trigger:** [#847 — Contract Strategy](https://github.com/DaveX2001/deliverable-tracking/issues/847) (comments 35-36)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

