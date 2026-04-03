---
publish: true
---

# Hippocampus Publishing: Auth + Access Control Evaluation
[[hippocampus-auth]]

Design evaluation for hippocampus publishing — authentication and access control via Cloudflare Pages + Access, with a two-phase path (Jekyll now, VitePress later). Vercel and Obsidian Publish evaluated and rejected.

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

The two options below replace the final output (GitHub Pages) while preserving the publish chain. Vercel and Obsidian Publish were evaluated and rejected — see Rejected Options.

**Decision: `publish: true` retirement.** With Cloudflare Access controlling who can view the site, the frontmatter filter becomes redundant. All content in the source repo deploys behind auth. This simplifies the pipeline: Workflow 1 copies all `.md` files instead of scanning for `publish: true`. Draft content is no longer a risk — unauthorized visitors can't reach any page.

### Option 2: Jekyll + Cloudflare Pages + Access

**What changes:** Hosting moves from GitHub Pages to Cloudflare Pages. Cloudflare Access sits in front as an authentication layer. Jekyll stays. Content stays. The `publish: true` frontmatter gate is retired — all content deploys behind auth.

**Auth model:** Cloudflare Access (Zero Trust) intercepts every request at the network edge:
- **Team:** Google OAuth — sign in with `@wilsch-ai.com`. Seamless.
- **Clients:** Email OTP (whitelist-only) — only emails explicitly added to the Access policy can authenticate. Non-whitelisted emails see "A code has been emailed to you" but receive nothing (deliberate security design — no information leakage about who is in the policy). Session configurable per-app (`session_duration` field — 30 min to 7 days, default 24h). No account creation needed.
- **Per-path isolation:** Access apps support `host/path/*` patterns natively. One deployment, different auth per `/project/traceline/` vs `/project/wilsch-ai/`. This enables multi-tenant on a single site.

**Session granularity:** Three-tier duration hierarchy:
- **Global:** 15 min to 1 month (default 24h) — how often users re-authenticate with their IdP across all apps
- **Per-app:** Override per Access application (same range)
- **Per-policy:** Override within an app — e.g., contractors get 24h sessions, team gets 7 days

Revocation is per-app (all sessions for one app) or per-user (one user across all apps). No per-user-per-app revocation — the one gap.

**OTP ceremony:** Client receives link via email or WhatsApp → opens in browser → redirected to CF Access login → enters email → receives 6-digit code via email → enters code → gets session cookie. When link comes via WhatsApp, client must switch to email app for the code — real friction (~30-60 seconds total). Corporate email scanners may auto-consume OTP codes (documented CF limitation; mitigation: allowlist `noreply@notify.cloudflare.com`).

**Agent automation (CLI/API/MCP):**

| Operation | Method |
|-----------|--------|
| Create project | `wrangler pages project create` |
| Deploy | `wrangler pages deploy _site/` (pre-build Jekyll first) |
| Custom domains | REST API: `POST /accounts/{id}/pages/projects/{name}/domains` |
| Config as code | `wrangler.toml` with `pages_build_output_dir` — becomes source of truth |
| Create Access app | REST API or CF MCP: `POST /accounts/{id}/access/apps` |
| Add client email | REST API or CF MCP: `PUT` policy's `include` array |
| Revoke user | REST API or CF MCP: `DELETE /accounts/{id}/access/users/{user_id}` |
| Enable OTP | REST API or CF MCP: `POST /accounts/{id}/access/identity_providers` type `onetimepin` |

**Cloudflare MCP server:** Official `cloudflare/mcp` repository provides an MCP server covering ~2,500 API endpoints including Access and Pages. The agent can manage auth policies, user access, and deployments natively through MCP — no raw REST calls needed. Wrangler CLI covers Pages deployment but does not cover Access management.

**Agent completeness: 100%.** Every operation — project creation, deploy, auth config, client management, revocation — is CLI, API, or MCP. No dashboard touch required.

**Push-to-deploy:** Connect the GitHub repo to Cloudflare Pages. Every push to `main` triggers a Jekyll build automatically. Preview deployments per branch. The existing two-workflow chain works — Cloudflare Pages builds from the pages repo.

**Migration:**
- Add `Gemfile` if not already committed
- Update `baseurl` in `_config.yml` — GitHub Pages uses `/public-wilsch-ai-pages`, Cloudflare uses `/`
- Add CNAME record at current DNS provider: `docs.wilsch-ai.com` → `<project>.pages.dev` (no DNS zone migration needed — see Domain & DNS Strategy)
- Remove `publish: true` frontmatter scanning from deploy workflow — all content goes live behind auth
- Custom layout, Mermaid.js, copyright footer — all transfer unchanged (static output)

**Cost:** Free. Cloudflare Pages (unlimited bandwidth, 500 builds/month per account) + Access Zero Trust (OTP included, no per-user cost). The widely-cited "50 user" limit is not confirmed in current CF docs — verify with Cloudflare support before treating as a hard constraint.

**Composability:** 100 Pages projects per account (soft limit). Full onboarding scriptable: create project → deploy → attach domain → create Access app → create policy → link policy. See Multi-Instance Architecture for the concrete design.

**Limitations:**
- 500 builds/month shared across ALL projects on the account
- Corporate email scanners may auto-consume OTP codes (mitigation: email allowlist)
- OTP code expires in 10 minutes
- Per-user-per-app revocation not supported (revoke is per-app or per-user globally)

**Effort:** ~1 hour setup. Zero content migration.

### Option 3: VitePress + Cloudflare Pages + Access

**What changes:** Both the SSG and hosting change. Jekyll is replaced by VitePress. Hosting moves to Cloudflare Pages with Access for auth. Content transfers; templates are rewritten as Vue components. The `publish: true` filter is already gone (retired in Phase 1).

**Why VitePress:** Purpose-built for documentation. Build time drops from 60-120s (Jekyll, 334 pages) to ~20s. Built-in local search via MiniSearch (Ctrl+K modal, 1 line of config). Instant HMR during development (sub-100ms vs Jekyll's 30-60s full rebuilds). Broken relative links surface as build errors — strict but catches accumulated dead links. The theme can be packaged as an npm module for reuse across instances.

**Search:** VitePress includes MiniSearch-powered full-text search out of the box. Activated with one config line: `themeConfig: { search: { provider: 'local' } }`. Appears as a modal dialog triggered by Ctrl+K from the nav bar. Supports fuzzy matching, custom rendering, and per-page exclusion via `search: false` frontmatter. For 334+ docs, the client-side index is substantial — Algolia DocSearch is available as a built-in alternative if performance needs scale (`provider: 'algolia'`).

**Plugin ecosystem:**

| Plugin | Purpose |
|--------|---------|
| `vitepress-plugin-mermaid` | Diagram rendering (already in use) |
| `vitepress-sidebar` | Auto-generates sidebar from file structure |
| `vitepress-export-pdf` | Export full site to PDF |
| `vite-pwa/vitepress` | Zero-config PWA (offline access) |
| `vitepress-plugin-search` | FlexSearch alternative to built-in MiniSearch |

No auth plugins exist in the VitePress ecosystem — auth is handled at the Cloudflare layer (platform-level, SSG-independent).

**Auth model:** Same as Option 2 — Cloudflare Access with whitelist-only email OTP (clients) + Google OAuth (team). Same CF MCP server for agent automation. The auth layer is platform-level, independent of the SSG.

**Push-to-deploy:** Same as Option 2 — Cloudflare Pages builds on push. Build command: `npm run docs:build`, output: `.vitepress/dist/`. Critical: disable HTML minification in Cloudflare Pages — it strips Vue hydration comments and causes client-side errors.

**Migration:**
- 334 markdown files transfer with frontmatter intact
- Liquid conversions: convert `include` tags to Vue SFC components, convert `raw` tags to `:::v-pre`, escape or wrap variables in `:::v-pre`
- Mermaid: `npm i vitepress-plugin-mermaid mermaid` + wrap config with `withMermaid()` — zero markdown changes needed
- Rewrite `default.html` as Vue theme component (~80 lines HTML+CSS → Vue SFC)
- `permalink:` frontmatter has no VitePress equivalent — files must be renamed to match desired URLs
- HTML strictness: VitePress validates bare `<`/`>` — long tail of small fixes across content files
- Set `ignoreDeadLinks: true` during migration, flip off to fix accumulated broken links

**Composability — npm theme packaging:** The theme is published as an npm package (`hippocampus-theme`) that exports a VitePress theme object and a base config. A new instance installs the package and extends it:

```
// .vitepress/theme/index.ts (3 lines)
import HippocampusTheme from 'hippocampus-theme'
export default HippocampusTheme

// .vitepress/config.ts (~15 lines)
import baseConfig from 'hippocampus-theme/config'
import { defineConfigWithTheme } from 'vitepress'

export default defineConfigWithTheme({
  extends: baseConfig,
  title: 'Instance Name',
  themeConfig: {
    logo: '/logo.png',
    siteTitle: 'Instance Docs',
    nav: [{ text: 'Guide', link: '/guide/' }]
  }
})
```

Per-instance customization: logo, title, nav, sidebar, colors (CSS variables), footer. Shared via theme: layout components, global styles, Mermaid plugin, search config. Total per-instance config: ~30-50 lines.

**Cost:** Free (same Cloudflare stack as Option 2). VitePress is open source.

**Limitations:**
- ~3-7 days migration effort (focused engineering; content ports as-is, work is in templates)
- Vue component knowledge helpful for theme customization (basic HTML/CSS sufficient for 80% of changes)
- Build tooling changes from Ruby (Bundler) to Node (npm/pnpm)
- Must disable Cloudflare HTML minification

**Effort:** ~3-7 days for migration + Cloudflare setup. Ongoing DX improvement over Jekyll.

### Rejected Options

Two additional platforms were evaluated and rejected.

**Vercel** — rejected due to cost and client friction:
- Minimum viable auth: $20/mo (clients must create Vercel accounts) or $170/mo (shared password)
- No email OTP — no low-friction client access path
- Individual user grants are dashboard-only (no API) — breaks agent automation requirement
- No per-path access control — multi-tenant requires separate projects at additional cost
- Advantage (DNS flexibility, best VitePress DX) does not offset the auth limitations

**Obsidian Publish** — rejected due to two hard blockers:
- No publishing API — content must be published through the Obsidian desktop app GUI. No headless, CLI, or API path exists. An AI agent cannot publish programmatically.
- No email OTP — static site password only (shared passwords for the entire site). No per-user auth, no SSO, no audit trail.
- Cost: $8-10/site/month. Custom domain supported (via Cloudflare), full-text search included, Mermaid native.
- The auth and automation gaps are fundamental — not workaround-able within the platform.

### Comparison

| Dimension | Current | Phase 1: Jekyll + CF | Phase 2: VitePress + CF |
|-----------|---------|---------------------|------------------------|
| **Auth** | None (public) | Email OTP (free, whitelist-only) | Email OTP (free, whitelist-only) |
| **Client friction** | Zero | OTP 30-60s, no account | OTP 30-60s, no account |
| **Agent automation** | 100% (push → done) | 100% (CLI + API + MCP) | 100% (CLI + API + MCP) |
| **Push-to-deploy** | Git push → auto | Git push → auto | Git push → auto |
| **SSG** | Jekyll | Jekyll | VitePress |
| **Search** | None | None (Jekyll limitation) | MiniSearch built-in (Ctrl+K) |
| **Content migration** | — | Zero | 3-7 days (from Jekyll) |
| **Setup effort** | — | ~1 hour | ~3-7 days |
| **Cost** | Free | Free | Free |
| **Per-path auth** | N/A | Yes (native) | Yes (native) |
| **Composability** | N/A | Per-path multi-tenant + scriptable | npm theme + separate projects |
| **DNS requirement** | GitHub | CNAME to .pages.dev (any DNS provider) | CNAME to .pages.dev (any DNS provider) |
| **Publish filter** | `publish: true` | Retired (auth replaces it) | N/A (never had it) |

### Two-Phase Path (Confirmed)

**Phase 1 — Now:** Move to Cloudflare Pages + Access. Keep Jekyll. Auth is live within hours. Zero content migration. Agent workflow changes minimally (update URL base in verify_publish.sh, update GHA workflow deploy target). The `publish: true` filter is retired — all content deploys behind auth.

**Phase 2 — Later:** Migrate Jekyll to VitePress. Same Cloudflare hosting and auth (no platform change). Migration effort: 3-7 days of focused engineering. Gains: 3-6x faster builds, built-in search (MiniSearch), instant HMR, npm-packageable theme for composability across company instances.

**Why this path works:** Cloudflare supports both Jekyll and VitePress. The SSG choice and the hosting/auth choice are independent. Moving hosting now doesn't lock in Jekyll — VitePress migration can happen whenever there's capacity.

### Domain & DNS Strategy

**Current URL:** `mariuswilsch.github.io/public-wilsch-ai-pages/`

**Target domain:** `docs.wilsch-ai.com` — consistent with the main website branding.

**DNS setup (no zone migration needed):** Cloudflare Pages supports custom domains via CNAME record from any DNS provider. No need to move `wilsch-ai.com` DNS to Cloudflare. Setup:
1. Create the Pages project on Cloudflare and associate `docs.wilsch-ai.com` as a custom domain
2. Add a CNAME record at the current DNS provider: `docs` → `<project>.pages.dev`
3. Cloudflare issues the SSL certificate automatically

**Important:** The custom domain must be associated in the Cloudflare Pages dashboard BEFORE adding the CNAME record at the external DNS provider. Adding the CNAME first results in a 522 error.

**New company instances:** Same pattern. Each company domain adds a CNAME for its docs subdomain (e.g., `docs.newcompany.com` → `<project>.pages.dev`). No DNS provider lock-in.

**Old links:** Moving platforms breaks `mariuswilsch.github.io/...` links. Mitigation options:
- Keep GitHub Pages active with Jekyll redirects to the new domain
- Accept the break — update shared links in email/WhatsApp threads
- Redirect via Jekyll plugin (if keeping Pages temporarily as redirect target)

### Multi-Instance Architecture

Each company gets its own hippocampus instance: separate repo, separate Cloudflare Pages project, separate Access app. Shared infrastructure (theme, CI/CD templates) is reused across instances.

**Phase 1 architecture (Jekyll):**

```
Company A (Wilsch AI)           Company B (New Company)
┌─────────────────────┐        ┌─────────────────────┐
│ source-repo-a/      │        │ source-repo-b/      │
│   hippocampus/      │        │   hippocampus/      │
│     global/         │        │     global/         │
│     project/        │        │     project/        │
│   _layouts/         │        │   _layouts/         │
│   _config.yml       │        │   _config.yml       │
└─────────┬───────────┘        └─────────┬───────────┘
          │ git push                     │ git push
          ▼                              ▼
┌─────────────────────┐        ┌─────────────────────┐
│ CF Pages Project A  │        │ CF Pages Project B  │
│ docs.wilsch-ai.com  │        │ docs.newco.com      │
│ + CF Access App A   │        │ + CF Access App B   │
│   (team + clients)  │        │   (team + clients)  │
└─────────────────────┘        └─────────────────────┘
```

Each instance is fully isolated: different content, different domain, different auth policies. The `_layouts/`, `_config.yml`, and deploy workflow are duplicated — in Phase 1 this is acceptable overhead for fast setup.

**Phase 2 architecture (VitePress + npm theme):**

```
hippocampus-theme (npm package)
├── Layout.vue          (shared)
├── styles/             (shared)
├── components/         (shared)
├── plugins/mermaid     (shared)
└── config.ts           (shared base config)
           │
    npm install
    ┌──────┴──────┐
    ▼              ▼
Instance A         Instance B
├── docs/          ├── docs/
│   global/        │   global/
│   project/       │   project/
├── .vitepress/    ├── .vitepress/
│   config.ts      │   config.ts      (~15 lines)
│   theme/         │   theme/
│     index.ts     │     index.ts     (3 lines)
├── public/        ├── public/
│   logo.png       │   logo.png       (per-company)
└── package.json   └── package.json
```

Shared: layout, styles, components, plugins, search config. Per-instance: logo, title, nav, sidebar, colors (~30-50 lines config).

**Onboarding a new company (scripted):**

Phase 1:
1. Clone template repo → update `_config.yml` (title, logo, copyright)
2. `wrangler pages project create <name>`
3. Set up GitHub integration or deploy workflow
4. Associate custom domain in CF Pages dashboard
5. Add CNAME at company's DNS provider
6. Create CF Access app via MCP/API (set email policies)
7. Verify: `verify_publish.sh`

Phase 2:
1. `npx vitepress init` → `npm install hippocampus-theme`
2. Write `config.ts` (~15 lines) + `theme/index.ts` (3 lines)
3. Same CF Pages + Access setup as Phase 1

**Isolation model:** Full isolation by default. Each instance has its own Git repository, Cloudflare Pages project, Cloudflare Access app (independent email policies), custom domain, and content (no cross-contamination).

Per-path Access apps on a single deployment remain available as a lightweight alternative for internal sub-divisions (e.g., different auth per `/project/client-a/` vs `/project/client-b/` within the same company instance).

### Scaling

**User growth:** Currently under 20 people need access. Could grow to 50 across all clients. Cloudflare's free tier user limit is unconfirmed in current docs — verify with Cloudflare support before treating as a hard constraint.

**Instance growth:** Each new company gets a separate CF Pages project (100 projects per account, soft limit). The Multi-Instance Architecture section describes the concrete setup. Current need: 2 instances (Wilsch AI + new company). Capacity: 100 projects on the free account.

**Build budget:** Cloudflare: 500 builds/month shared across all projects. With 2-3 instances and normal publishing cadence (~5-10 publishes/day across all instances), this is well within limits. Monitor as instances multiply.

---

## Source

- **Issue:** [#1217 — Evaluate Vercel for Hippocampus Publishing](https://github.com/DaveX2001/deliverable-tracking/issues/1217)
- **Trigger:** [#847 Comment 35-36](https://github.com/DaveX2001/deliverable-tracking/issues/847#issuecomment-4098610248) — copyright/IP discussion surfaced that published artifacts have no access control
- **Sessions:**
  - JA extraction pass 1 (2026-04-01): session `3aa18eda-a131-488b-ab55-3d802f295edd`
  - JA extraction pass 2 (2026-04-02): session `563457e9-ba31-4e9a-ad19-8def6f199cfb` — Marius feedback incorporated, deep platform research, composability analysis
  - JA extraction pass 3 (2026-04-03): session `d483109e-c8f5-40be-a02f-f22c2b932f0e` — Marius decisions incorporated (Vercel rejected, Obsidian rejected, publish filter retired), DNS CNAME discovery, CF MCP server, multi-instance architecture, OTP whitelist-only confirmation
- **Investigated repos:** `veloxforce/claude-user-configs` (hippocampus source + deploy workflow), `MariusWilsch/public-wilsch-ai-pages` (pages output + deploy workflow)
- **Platform docs:** [Cloudflare Pages](https://developers.cloudflare.com/pages/), [CF Access OTP](https://developers.cloudflare.com/cloudflare-one/identity/one-time-pin/), [CF Access Session Management](https://developers.cloudflare.com/cloudflare-one/identity/users/session-management/), [CF Pages Custom Domains](https://developers.cloudflare.com/pages/configuration/custom-domains/), [CF MCP Server](https://github.com/cloudflare/mcp), [VitePress](https://vitepress.dev/), [VitePress Search](https://vitepress.dev/reference/default-theme-search), [vitepress-plugin-mermaid](https://github.com/emersonbottero/vitepress-plugin-mermaid), [Obsidian Publish](https://obsidian.md/publish)
- **Related issues:** [#1349 — New Company Formation](https://github.com/DaveX2001/deliverable-tracking/issues/1349)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

