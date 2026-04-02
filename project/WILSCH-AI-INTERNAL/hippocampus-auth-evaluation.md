---
publish: true
---

# Hippocampus Publishing: Auth + Access Control Evaluation
[[hippocampus-auth]]

Evaluation of publishing alternatives for hippocampus — adding authentication and access control while preserving the push-to-deploy workflow.

---

## Problem Statement

All 334 published hippocampus documents are publicly accessible at `mariuswilsch.github.io/public-wilsch-ai-pages/`. There is no authentication — anyone with the URL can read every design doc, proposal, and methodology document. Additionally, a new company is forming (IMIF framework, #1349) that will need its own independent hippocampus instance with the same workflow. The current architecture is hardcoded to one repository, one domain, one company identity.

Design documents are sensitive IP even when shared with specific clients. The current workflow — grab a link, send it — works well for speed but provides zero access control. Equally important: the AI agent that publishes docs should manage everything (deploy, auth, sharing) without Marius touching a dashboard. Today's workflow is fully agent-driven. The replacement must match that standard.

Copyright protection (© 2026 + CC BY-NC-ND 4.0 via CI) prevents copying but does not control access. Authentication controls who can see the content.

**Preconditions:**
- The hippocampus contains proprietary methodology, contract strategies, and client-specific proposals that constitute competitive advantage
- Clients currently receive links to published docs and read them in the browser
- The team (Marius, David, AI agents) uses published docs as working artifacts reviewed via Speechify
- The `publish: true` frontmatter gate controls what gets published, but all published content is equally public
- A new company (separate from Wilsch AI) will need its own hippocampus instance — same workflow, different branding, different domain
- Links are shared via both email and WhatsApp — auth friction matters differently per channel

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Published hippocampus documents are private by default with controlled sharing for clients. The agent manages the full lifecycle (publish, auth, share) without dashboard interaction. The workflow stamps out new instances per company. |
| **Success** | Team publishes and reads docs without friction. Clients access shared docs within 60 seconds via email OTP or equivalent — no account creation. The agent adds new client access via CLI/API. A second hippocampus instance can be created by running a script. |
| **Done test** | (1) Can someone without credentials access a published doc via direct URL? If NO → access control works. (2) Can Marius send a doc link and the client reads it within 60 seconds? If YES → sharing friction is acceptable. (3) Can the agent add a new client email without Marius opening a dashboard? If YES → agent automation works. (4) Can a new company instance be created by running a parameterized script? If YES → composability works. |

---

## Approach

### Current Architecture

The hippocampus publishing chain spans two repositories and two GitHub Actions workflows:

1. **Source:** `veloxforce/claude-user-configs` contains `hippocampus/` with all markdown content. Authors set `publish: true` in YAML frontmatter and push.
2. **Workflow 1** (`deploy-hippocampus.yml`): Scans all `.md` files for `publish: true`, copies matches + referenced assets, clones `MariusWilsch/public-wilsch-ai-pages`, clears it, syncs content + theme files, appends copyright footer, pushes.
3. **Workflow 2** (`deploy.yml` in pages repo): Jekyll build → GitHub Pages deploy.
4. **Verification:** `verify_publish.sh` polls both workflows, checks ETag freshness via curl, opens the published URL in the browser.

**Content structure:**
- `global/` (164 files) — methodology, position agreements, org chart, ADRs
- `project/` (18 client directories) — proposals, design docs, meeting agendas

**Layout:** Custom `default.html` with Wilsch AI branding, Mermaid.js loaded via CDN, minimal Jekyll config (no theme).

**Composability analysis:** The pipeline is already 80% composable. The frontmatter filter, asset copier, and verification script are platform-agnostic. Only five items are hardcoded per instance: source repo path, target repo, PAT secret, copyright text, and branding in `default.html`. The platform choice affects only the deploy step — everything upstream stays identical.

**Agent workflow:** `verify_publish.sh` handles the full cycle: commit → push → poll both workflows → verify ETag freshness → open browser. The script doesn't care how deployment happens — it commits, pushes, polls, and verifies. Switching platforms means updating the GHA workflow and the URL base; the agent's experience barely changes.

The four options below replace the final output (GitHub Pages) while preserving the publish chain.

### Option 2: Jekyll + Cloudflare Pages + Access

**What changes:** Hosting moves from GitHub Pages to Cloudflare Pages. Cloudflare Access sits in front as an authentication layer. Jekyll stays. Content stays. Frontmatter stays.

**Auth model:** Cloudflare Access (Zero Trust) intercepts every request at the network edge:
- **Team:** Google OAuth — sign in with `@wilsch-ai.com`. Seamless.
- **Clients:** Email OTP — client enters their email, receives a 6-digit code (not a clickable link), enters it. Session configurable per-app (`session_duration` field — 30 min to 7 days, default 24h). No account creation needed.
- **Per-path isolation:** Access apps support `host/path/*` patterns natively. One deployment, different auth per `/project/traceline/` vs `/project/wilsch-ai/`. This enables multi-tenant on a single site.

**OTP ceremony:** Client receives link via email or WhatsApp → opens in browser → redirected to CF Access login → enters email → receives 6-digit code via email → enters code → gets session cookie. When link comes via WhatsApp, client must switch to email app for the code — real friction (~30-60 seconds total). Corporate email scanners may auto-consume OTP codes (documented CF limitation; mitigation: allowlist `noreply@notify.cloudflare.com`).

**Agent automation (CLI/API):**

| Operation | Method |
|-----------|--------|
| Create project | `wrangler pages project create` |
| Deploy | `wrangler pages deploy _site/` (pre-build Jekyll first) |
| Custom domains | REST API: `POST /accounts/{id}/pages/projects/{name}/domains` |
| Config as code | `wrangler.toml` with `pages_build_output_dir` — becomes source of truth |
| Create Access app | REST API: `POST /accounts/{id}/access/apps` |
| Add client email | REST API: `PUT` policy's `include` array |
| Revoke user | REST API: `DELETE /accounts/{id}/access/users/{user_id}` |
| Enable OTP | REST API: `POST /accounts/{id}/access/identity_providers` type `onetimepin` |

**Agent completeness: 100%.** Every operation — project creation, deploy, auth config, client management, revocation — is CLI or API. No dashboard touch required.

**Push-to-deploy:** Connect the GitHub repo to Cloudflare Pages. Every push to `main` triggers a Jekyll build automatically. Preview deployments per branch. The existing two-workflow chain works — Cloudflare Pages builds from the pages repo.

**Migration:**
- Add `Gemfile` if not already committed
- Update `baseurl` in `_config.yml` — GitHub Pages uses `/public-wilsch-ai-pages`, Cloudflare uses `/`
- DNS must move to Cloudflare (required for Access to intercept traffic)
- Custom layout, Mermaid.js, copyright footer — all transfer unchanged (static output)

**Cost:** Free. Cloudflare Pages (unlimited bandwidth, 500 builds/month per account) + Access Zero Trust (OTP included, no per-user cost). The widely-cited "50 user" limit is not confirmed in current CF docs — verify with Cloudflare support before treating as a hard constraint.

**Composability:** 100 Pages projects per account (soft limit). Full onboarding scriptable: create project → deploy → attach domain → create Access app → create policy → link policy. Per-path Access apps enable multi-tenant on a single deployment — one hippocampus site, different auth per client path.

**Limitations:**
- DNS must be on Cloudflare — non-negotiable for Access. If the new company has its own domain, that domain's DNS also needs CF.
- 500 builds/month shared across ALL projects on the account
- Corporate email scanners may auto-consume OTP codes (mitigation: email allowlist)
- OTP code expires in 10 minutes

**Effort:** ~1 hour setup. Zero content migration.

### Option 3: VitePress + Cloudflare Pages + Access

**What changes:** Both the SSG and hosting change. Jekyll is replaced by VitePress. Hosting moves to Cloudflare Pages with Access for auth. Content transfers; templates are rewritten as Vue components.

**Why VitePress:** Purpose-built for documentation. Build time drops from 60-120s (Jekyll, 334 pages) to ~20s. Built-in local search (zero-config). Instant HMR during development (sub-100ms vs Jekyll's 30-60s full rebuilds). Broken relative links surface as build errors — strict but catches accumulated dead links. The theme can be packaged as an npm module for reuse across instances.

**Auth model:** Same as Option 2 — Cloudflare Access with Google OAuth (team) + email OTP (clients). The auth layer is platform-level, independent of the SSG.

**Push-to-deploy:** Same as Option 2 — Cloudflare Pages builds on push. Build command: `npm run docs:build`, output: `.vitepress/dist/`. Critical: disable HTML minification in Cloudflare Pages — it strips Vue hydration comments and causes client-side errors.

**Migration:**
- 334 markdown files transfer with frontmatter intact
- Liquid conversions: convert `include` tags to Vue SFC components, convert `raw` tags to `` `:::v-pre` ``, escape or wrap variables in `` `:::v-pre` ``
- `publish: true` filtering: requires ~20-line pre-build Node script (VitePress has no built-in publish filter). Script reads frontmatter, generates `srcExclude` list
- Mermaid: `npm i vitepress-plugin-mermaid mermaid` + wrap config with `withMermaid()` — zero markdown changes needed
- Rewrite `default.html` as Vue theme component (~80 lines HTML+CSS → Vue SFC)
- `permalink:` frontmatter has no VitePress equivalent — files must be renamed to match desired URLs
- HTML strictness: VitePress validates bare `<`/`>` — long tail of small fixes across content files
- Set `ignoreDeadLinks: true` during migration, flip off to fix accumulated broken links

**Composability:** The theme can be published as an npm package (`hippocampus-theme`). New instance = `npx vitepress init` + install theme + 30-50 lines config (title, logo, nav). All layout, components, and CSS shared via the package. Per-instance config is minimal.

**Cost:** Free (same Cloudflare stack as Option 2). VitePress is open source.

**Limitations:**
- ~3-7 days migration effort (focused engineering; content ports as-is, work is in templates)
- Vue component knowledge helpful for theme customization (basic HTML/CSS sufficient for 80% of changes)
- Build tooling changes from Ruby (Bundler) to Node (npm/pnpm)
- Must disable Cloudflare HTML minification

**Effort:** ~3-7 days for migration + Cloudflare setup. Ongoing DX improvement over Jekyll.

### Option 4: Vercel

**What changes:** Hosting moves to Vercel. Jekyll stays (Vercel supports Jekyll natively with auto-detection). Vercel provides deployment protection for auth.

**Why Vercel:** Best-in-class CLI (`vercel`), native VitePress support for the future upgrade path, and the most polished developer experience. Vercel's GitHub integration auto-deploys on push. For a "Jekyll now, VitePress later" strategy, Vercel is the platform that's most natural for the VitePress phase.

**Auth model — the critical constraint:**
- **Hobby (free):** Production is always public. Only preview URLs are protectable. Max 1 external user. Not viable for client-facing protected docs.
- **Pro ($20/mo):** Vercel Authentication on all deployments — but clients must create a Vercel account to access. No email OTP, no email allowlisting, no self-serve invites.
- **Password protection:** Requires Pro + Advanced Deployment Protection add-on = **$170/mo total**. Single shared password per project (no per-user passwords).
- **Per-path protection:** Not supported. One auth scope per project — no multi-tenant on a single deployment.
- **Shareable Links:** Available on all plans as a bypass — anyone with the link skips auth. Useful as a workaround but defeats the purpose of access control.

**OTP ceremony:** Vercel has no email OTP. The only zero-account option is password protection at $170/mo. With Vercel Authentication, the client flow is: receive link → open → redirected to Vercel login → create Vercel account (if new) → request access → Marius approves in dashboard → client accesses. This is significantly more friction than Cloudflare's OTP.

**Agent automation (CLI/API):**

| Operation | Method |
|-----------|--------|
| Create project | `vercel project add` / REST API `POST /v11/projects` |
| Deploy | `vercel --prod` / `vercel deploy --prod --token $TOKEN` |
| Custom domains | `vercel domains add` |
| Config as code | `vercel.json` (build settings only — auth NOT configurable in vercel.json) |
| Enable auth | REST API: `PATCH /v9/projects/{id}` with `ssoProtection` or `passwordProtection` |
| Add/remove user access | **Dashboard only** — no documented API for granting individual access |

**Agent completeness: ~85%.** Deploy, config, domains, and auth toggles are scriptable. But granting/revoking individual user access requires the dashboard — the gap that breaks full automation.

**Push-to-deploy:** Connect GitHub repo to Vercel. Auto-deploys on push. Jekyll is auto-detected; no `vercel.json` required for basic setup.

**Migration:**
- Zero content migration — Vercel builds Jekyll natively
- Optional `vercel.json` for build customization
- DNS: works with any provider (no Cloudflare-style lock-in)

**Cost:**
- Hobby (free): not viable for protected production docs
- Pro ($20/mo): Vercel Auth on all deployments (clients need Vercel accounts)
- Pro + Advanced ($170/mo): password protection (shared password, no accounts needed)

**Composability:** 200 projects on Hobby, unlimited on Pro. Full project creation scriptable via API. But each project = one auth scope — no per-path policies. Multi-tenant requires separate Vercel projects per client/company.

**Limitations:**
- Auth on free tier is effectively useless for this use case (production always public)
- Minimum viable auth = $20/mo (requires client Vercel accounts) or $170/mo (password)
- No per-path access control — separate projects needed for multi-tenant
- Individual user grants are dashboard-only (no API)
- No email OTP — clients must create platform accounts or use shared passwords

**Effort:** ~30 minutes setup. Zero content migration. DNS flexibility (any provider).

### Comparison

| Dimension | 1: Current | 2: Jekyll + CF | 3: VitePress + CF | 4: Vercel |
|-----------|-----------|---------------|-------------------|-----------|
| **Auth** | None (public) | Email OTP (free) | Email OTP (free) | Vercel accounts ($20/mo) or password ($170/mo) |
| **Client friction** | Zero | OTP 30-60s, no account | OTP 30-60s, no account | Create Vercel account or enter shared password |
| **Agent automation** | 100% (push → done) | 100% (CLI + API) | 100% (CLI + API) | ~85% (user grants = dashboard) |
| **Push-to-deploy** | Git push → auto | Git push → auto | Git push → auto | Git push → auto |
| **Jekyll support** | Native | Native | N/A (VitePress) | Native |
| **VitePress support** | N/A | Supported (disable minification) | Native | Native (best DX) |
| **Content migration** | — | Zero | 3-7 days | Zero |
| **Setup effort** | — | ~1 hour | ~3-7 days | ~30 minutes |
| **Cost** | Free | Free | Free | $20-170/mo |
| **Per-path auth** | N/A | Yes (native) | Yes (native) | No (project-scoped) |
| **Composability** | N/A | Per-path multi-tenant + 100 projects | npm theme + per-path | Separate projects only |
| **DNS requirement** | GitHub | Cloudflare DNS | Cloudflare DNS | Any DNS |
| **Scaling (users)** | N/A | Free tier (verify limit with CF) | Same as Option 2 | 1 on Hobby, unlimited on Pro |

### Two-Phase Path

Marius identified a deployment path: start with Option 2 (Jekyll + Cloudflare) for immediate auth, upgrade to Option 3 (VitePress + Cloudflare) later for DX improvement.

**Phase 1 — Now:** Move to Cloudflare Pages + Access. Keep Jekyll. Auth is live within hours. Zero content migration. Agent workflow changes minimally (update URL base in verify_publish.sh, update GHA workflow deploy target).

**Phase 2 — Later:** Migrate Jekyll to VitePress. Same Cloudflare hosting and auth (no platform change). Migration effort: 3-7 days of focused engineering. Gains: 3-6x faster builds, built-in search, instant HMR, npm-packageable theme for composability.

**Why this path works:** Cloudflare supports both Jekyll and VitePress. The SSG choice and the hosting/auth choice are independent. Moving hosting now doesn't lock in Jekyll — VitePress migration can happen whenever there's capacity.

**Vercel alternative path:** Start with Vercel (Jekyll), upgrade to VitePress later on the same platform. Vercel is the most natural home for Vite-based sites. But auth cost ($20-170/mo) and client friction (Vercel accounts or shared passwords) are the tradeoffs.

### Domain & DNS Strategy

**Current URL:** `mariuswilsch.github.io/public-wilsch-ai-pages/`

**Cloudflare path (Options 2 & 3):** DNS must move to Cloudflare. Default URL: `something.pages.dev`. Custom domain (e.g., `docs.wilsch-ai.com`) requires `wilsch-ai.com` DNS on Cloudflare. If the new company has its own domain, that domain also needs Cloudflare DNS.

**Vercel path (Option 4):** Works with any DNS provider. Default URL: `something.vercel.app`. Custom domain via CNAME — no DNS migration needed.

**Old links:** Moving platforms breaks `mariuswilsch.github.io/...` links. Mitigation: keep GitHub Pages active as a redirect target, or accept the break and update shared links.

**Undefined:** Which domain to use and whether DNS migration to Cloudflare is acceptable. → [Meeting Agenda](hippocampus-auth-meeting-agenda)

### Scaling

**User growth:** Currently under 20 people need access. Could grow to 50 across all clients. Cloudflare's free tier user limit is unconfirmed in current docs — verify before committing. Vercel Pro supports unlimited users but at $20/mo minimum.

**Instance growth:** The new company (#1349) will need its own hippocampus instance. Cloudflare supports this via per-path Access apps (one deployment, multiple auth scopes) or separate projects (100 per account). Vercel requires separate projects (200 on Hobby).

**Build budget:** Cloudflare: 500 builds/month shared across all projects. Vercel: 100 deployments/day on Hobby. Both sufficient for current usage but worth monitoring as instances multiply.

---

## Source

- **Issue:** [#1217 — Evaluate Vercel for Hippocampus Publishing](https://github.com/DaveX2001/deliverable-tracking/issues/1217)
- **Trigger:** [#847 Comment 35-36](https://github.com/DaveX2001/deliverable-tracking/issues/847#issuecomment-4098610248) — copyright/IP discussion surfaced that published artifacts have no access control
- **Sessions:**
  - JA extraction pass 1 (2026-04-01): session `3aa18eda-a131-488b-ab55-3d802f295edd`
  - JA extraction pass 2 (2026-04-02): session `563457e9-ba31-4e9a-ad19-8def6f199cfb` — Marius feedback incorporated, deep platform research, composability analysis
- **Investigated repos:** `veloxforce/claude-user-configs` (hippocampus source + deploy workflow), `MariusWilsch/public-wilsch-ai-pages` (pages output + deploy workflow)
- **Platform docs:** [Cloudflare Pages](https://developers.cloudflare.com/pages/), [CF Access OTP](https://developers.cloudflare.com/cloudflare-one/identity/one-time-pin/), [Vercel Deployment Protection](https://vercel.com/docs/security/deployment-protection), [VitePress](https://vitepress.dev/), [vitepress-plugin-mermaid](https://github.com/emersonbottero/vitepress-plugin-mermaid)
- **Related issues:** [#1349 — New Company Formation](https://github.com/DaveX2001/deliverable-tracking/issues/1349)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

