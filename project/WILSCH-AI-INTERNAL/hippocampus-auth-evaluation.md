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
- **Per-path isolation:** Access apps support `host/path/*` patterns natively. The more specific path wins — a broad `docs.wilsch-ai.com/*` policy for team access stacks correctly with narrow per-client policies. This is more granular than GitHub's repository-level access model: per-path auth achieves per-client isolation within a single deployment, without splitting repositories or projects.

  **Concrete example:**

  | Access App | Path Pattern | Policy | Auth |
  |------------|-------------|--------|------|
  | Team (broad) | `/*` | Allow `@wilsch-ai.com` | Google OAuth |
  | Traceline | `/project/traceline/*` | Allow `client@traceline.de` | Email OTP |
  | Reichert | `/project/abtmayr-reichert/*` | Allow `r.reichert@example.com` | Email OTP |

  Traceline client navigating to `/project/abtmayr-reichert/` is blocked — Cloudflare shows a branded "Access Denied" page (customizable: default CF page, custom redirect URL on free tier, or custom HTML on paid tier). The denial page can be replaced with a Worker-based 404 response if path-existence concealment is needed.

  **Cookie behavior:** The `CF_Authorization` cookie is domain-scoped by default. A user authenticates once and Access evaluates their identity against each path's policy — the cookie carries identity, the policy grants or denies per path. A consultant in both Traceline and Reichert whitelists authenticates once and can access both. Optional: enable `Cookie Path Attribute` for per-path cookie scoping (separate auth per path).

  **User management:** Each Access app has its own email policy. Access Groups can DRY up common whitelists (e.g., a "wilsch-ai-internal" group reused across all per-path apps).

**Session granularity:** Three-tier duration hierarchy:
- **Global:** 15 min to 1 month (default 24h) — how often users re-authenticate with their IdP across all apps
- **Per-app:** Override per Access application (same range)
- **Per-policy:** Override within an app — e.g., contractors get 24h sessions, team gets 7 days

Revocation is per-app (all sessions for one app) or per-user (one user across all apps). No per-user-per-app revocation — the one gap.

**OTP ceremony:** Client receives link via email or WhatsApp → opens in browser → redirected to CF Access login → enters email → receives 6-digit code via email → enters code → gets session cookie. When link comes via WhatsApp, client must switch to email app for the code — real friction (~30-60 seconds total). Corporate email scanners may auto-consume OTP codes (documented CF limitation; mitigation: allowlist `noreply@notify.cloudflare.com`).

**Agent automation preference hierarchy:**

The agent must prefer MCP over CLI, and CLI over API. API is the fallback for operations not covered by MCP or CLI.

| Preference | Tool | Covers |
|------------|------|--------|
| **1st: MCP** | Cloudflare MCP server (`cloudflare/mcp`) | Access apps, policies, user management, identity providers, Pages config (~2,500 endpoints) |
| **2nd: CLI** | Wrangler CLI | Pages project creation, deployment, `wrangler.toml` config-as-code |
| **3rd: API** | REST API | Custom domains (`POST .../domains`), edge cases not covered by MCP or CLI |

| Operation | Preferred | Command |
|-----------|-----------|---------|
| Create Access app | MCP | `POST /accounts/{id}/access/apps` |
| Add client email | MCP | `PUT` policy `include` array |
| Revoke user | MCP | `DELETE /accounts/{id}/access/users/{user_id}` |
| Enable OTP | MCP | `POST /accounts/{id}/access/identity_providers` type `onetimepin` |
| Create Pages project | CLI | `wrangler pages project create` |
| Deploy | CLI | `wrangler pages deploy _site/` |
| Config as code | CLI | `wrangler.toml` with `pages_build_output_dir` |
| Custom domains | API | `POST /accounts/{id}/pages/projects/{name}/domains` |

**Cloudflare MCP server:** Official `cloudflare/mcp` repository. The agent manages auth policies, user access, and deployments natively through MCP. Wrangler CLI covers Pages deployment and project setup. REST API is the fallback for operations not yet exposed via MCP or CLI (currently: custom domain association).

**Agent completeness: 100%.** Every operation — project creation, deploy, auth config, client management, revocation — is MCP, CLI, or API. No dashboard touch required.

**Access management — Cloudflare role spec:**

Operations required to manage a hippocampus instance:

| Operation | CF Product | Permission |
|-----------|-----------|------------|
| Create/deploy Pages project | Workers/Pages | Write |
| Create/edit Access app | Zero Trust Access | Write |
| Create/edit Access policy | Zero Trust Access | Write |
| Add/remove client email from whitelist | Zero Trust Access | Write |
| Custom domain / CNAME configuration | DNS (domain-scoped) | Write |
| View deployment logs | Workers/Pages | Read |

**User role (least-privilege combination):**

| Role | Scope | Covers |
|------|-------|--------|
| `Workers Platform Admin` | Account | Pages create/deploy/manage, logs read |
| `Cloudflare Access` | Account | Access apps, policies, email whitelist management |
| `Domain DNS` | Domain-scoped (e.g., `docs.wilsch-ai.com`) | Custom domain CNAME configuration |

This combination is the minimum-viable set of stable roles for the operations above. Resource-scoped `Cloudflare Access App Admin` and `Cloudflare Access Policy Admin` roles (Beta) offer tighter blast-radius if needed later.

**MCP automation uses a separate API token — not the user session.** The Claude agent invoking the Cloudflare MCP server authenticates via API token, scoped independently of any member's role. The automation token should be narrower than the user role: restrict it to specific Pages project IDs and Access app IDs rather than account-wide. The user role remains available for dashboard and emergency access outside the MCP flow.

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

**Search evaluation:**

Four search providers were evaluated for a 334-doc private site behind Cloudflare Access. The auth constraint is decisive: any solution that crawls a live URL fails against CF Access unless injecting Service Token credentials. Build-time indexing from source files is the safe path.

| Provider | Type | CF Access Compatible | Integration | Status |
|----------|------|---------------------|-------------|--------|
| **MiniSearch** (built-in) | Client-side, build-time index | Yes (no crawler) | 1 config line | Active (VitePress core) |
| **Typesense** | Server-side, build-time index | Yes (`vitepress-plugin-typesense`) | ~15 lines + server | Active (v1.1.5, Feb 2026) |
| **Algolia DocSearch** | Server-side, crawler | No (free tier requires public site; paid tier needs CF Service Token) | Built-in provider | Rejected |
| **FlexSearch** | Client-side, build-time index | Yes (no crawler) | Community plugin | Rejected (abandoned Apr 2023) |

**Decision: MiniSearch as default, Typesense as upgrade path.**

MiniSearch ships with VitePress — zero dependencies, 1 config line: `themeConfig: { search: { provider: 'local' } }`. Ctrl+K modal, fuzzy matching, per-page exclusion via `search: false` frontmatter. At 334 docs (2-5 KB each), the client-side index is well within MiniSearch's comfortable range.

Typesense is the upgrade path if search quality needs typo tolerance, relevance ranking, or faceted filtering beyond MiniSearch's capabilities. `vitepress-plugin-typesense` builds the index at `vitepress build` time (no crawler, no CF Access friction). Requires one Docker container (self-hosted, free) or Typesense Cloud ($7-58/month). Evaluate when build time exceeds 2 minutes or search quality requirements grow.

**Algolia rejected:** Free DocSearch tier explicitly requires public sites. The crawler cannot authenticate through CF Access without a paid plan + CF Service Token configuration. Paid Algolia ($50-200/month) is disproportionate for a private 334-doc site.

**FlexSearch rejected:** `vitepress-plugin-search` (the FlexSearch wrapper) has no stable release — last published April 2023 (alpha), 8 unresolved issues, no VitePress 1.x compatibility verification. Do not use.

**Plugin ecosystem:**

| Plugin | Purpose |
|--------|---------|
| `vitepress-plugin-mermaid` | Diagram rendering (already in use) |
| `vitepress-sidebar` | Auto-generates sidebar from file structure |
| `vitepress-export-pdf` | Per-page and full-site PDF export |
| `vite-pwa/vitepress` | Zero-config PWA (offline access) |
| `vitepress-plugin-typesense` | Typesense search integration (upgrade path) |

**PDF export:** `vitepress-export-pdf` generates PDF companions at build time via Puppeteer (headless Chrome). Each page gets a downloadable PDF with Mermaid diagrams rendered correctly (JavaScript executes during PDF generation). Distribution: a "Download PDF" button on each doc page, served at the same path with `.pdf` extension — protected by the same CF Access policy as the HTML version. Build time impact: ~2-5 minutes added to CI for 334 pages. Can be scoped to `project/` only (client-facing docs) if `global/` PDFs are unnecessary.

**Sidebar navigation:** VitePress supports multi-sidebar — different sidebar trees per URL path prefix. Combined with per-path auth, this creates clean client isolation at both access and navigation layers:

```ts
sidebar: {
  '/project/traceline/': [
    // Only Traceline docs visible in sidebar
  ],
  '/project/abtmayr-reichert/': [
    // Only Reichert docs visible in sidebar
  ],
  '/global/': [
    // Methodology, position agreements — team only
  ]
}
```

A Traceline client at `/project/traceline/` sees only Traceline entries in the sidebar. They never encounter other clients' content or directory names in navigation. `vitepress-sidebar` auto-generates these sidebar trees from the directory structure — no manual listing of individual pages required.

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
| **Agent automation** | 100% (push → done) | 100% (MCP → CLI → API) | 100% (MCP → CLI → API) |
| **Agent preference** | N/A | MCP → CLI → API | MCP → CLI → API |
| **Push-to-deploy** | Git push → auto | Git push → auto | Git push → auto |
| **SSG** | Jekyll | Jekyll | VitePress |
| **Search** | None | None (Jekyll limitation) | MiniSearch default, Typesense upgrade |
| **PDF export** | N/A | N/A | Per-page download (build-time) |
| **Sidebar** | N/A | N/A | Multi-sidebar (per-path isolation) |
| **Content migration** | — | Zero | 3-7 days (from Jekyll) |
| **Setup effort** | — | ~1 hour | ~3-7 days |
| **Cost** | Free | Free | Free |
| **Per-path auth** | N/A | Yes (native) | Yes (native) |
| **Client versioning** | N/A | Branch + main (stable URL, CF-native) | Branch + main (stable URL, CF-native) |
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
2. Add a CNAME record at the current DNS provider: `docs` → `<project>.pages.dev` (Cloudflare Pages default domain — every Pages project gets a `<project>.pages.dev` subdomain automatically)
3. Cloudflare issues the SSL certificate automatically

**Important:** The custom domain must be associated in the Cloudflare Pages dashboard BEFORE adding the CNAME record at the external DNS provider. Adding the CNAME first results in a 522 error.

**New company instances:** Same pattern. Each company domain adds a CNAME for its docs subdomain (e.g., `docs.newcompany.com` → `<project>.pages.dev`). No DNS provider lock-in.

**Migration strategy:** Run both systems in parallel until the Done Test passes (see Success Definition). GitHub Pages stays live at `mariuswilsch.github.io/...` while Cloudflare Pages runs at `docs.wilsch-ai.com`. When all four Done Test criteria are confirmed, GitHub Pages is decommissioned and old links break. The team accepts the link breakage — there are few shared links in circulation, and impacted recipients will be notified directly.

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

### Client Versioning

Clients receive a stable URL (`docs.wilsch-ai.com/project/<client>/...`) that reflects the current state of the `main` branch. Drafting happens on feature branches with team-only preview access; explicit merge to `main` promotes the version the client sees. This is Cloudflare Pages' native production-branch model.

**Mechanism:** Cloudflare Pages deploys the configured production branch (`main`) to the custom domain. Every non-production branch auto-deploys to a preview URL (`<hash>.<project>.pages.dev`). Preview URLs are protected by a single project-level Access toggle (team-only policy). The custom domain is protected by a separate Access app with per-path client policies.

**Workflow:**
1. Team creates a feature branch for new or updated content (e.g., `docs/reichert-update`)
2. Push → Cloudflare auto-deploys to a preview URL (team-only Access)
3. Team reviews the preview (Speechify, internal feedback)
4. Merge to `main` → Cloudflare deploys to `docs.wilsch-ai.com`
5. Client's stable URL automatically reflects the new version on next visit

**Why this model solves all three client-sharing concerns:**
- **Stable URL:** client bookmarks once; no re-sharing friction
- **Drafting privacy:** feature branches sit behind the team-only preview Access policy; clients cannot reach in-progress work
- **Release-gate timing:** `git merge → main` is the explicit promotion act — updates reach clients only when the team pushes

**Two-Access-app model (CF Pages requirement):**

| App | Scope | Policy | Purpose |
|-----|-------|--------|---------|
| Preview Access (project-level toggle) | All `*.pages.dev` previews for the project | Team-only (`@wilsch-ai.com`) | Protects in-progress work across every branch |
| Production Access (custom domain) | `docs.wilsch-ai.com` | Per-path client policies + team | What clients see |

The project-level preview toggle applies uniformly to every preview URL — no per-branch configuration required.

**Trade-off:** No version pinning. Once merged to `main`, the client sees the latest content on their next visit. If a client explicitly needs to stay on a prior version (e.g., legal archive, "the version attached to the contract"), a client-specific branch deployed to a separate URL remains available as a CF-native pattern — not the default path, added only if the need materializes.

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
  - JA extraction pass 4 (2026-04-04): session `be03dcb5-f1dc-4104-bf44-ea5c4ab3adc1` — Marius April 4 feedback: search design pass (MiniSearch/Typesense/Algolia/FlexSearch evaluated), client versioning (snapshot URLs), PDF export, sidebar navigation, per-path auth expansion, agent preference hierarchy, migration decision
  - JA extraction pass 5 (2026-04-05): session `ec0fd284-3b20-4ee3-a638-ce676b1429a1` — Marius April 5 feedback: client versioning rewritten (branch + main model, snapshot URL retired), CF role spec added (user role + API token), two-Access-app model confirmed
- **Investigated repos:** `veloxforce/claude-user-configs` (hippocampus source + deploy workflow), `MariusWilsch/public-wilsch-ai-pages` (pages output + deploy workflow)
- **Platform docs:** [Cloudflare Pages](https://developers.cloudflare.com/pages/), [CF Pages Preview Deployments](https://developers.cloudflare.com/pages/configuration/preview-deployments/), [CF Pages Branch Build Controls](https://developers.cloudflare.com/pages/configuration/branch-build-controls/), [CF Account Roles](https://developers.cloudflare.com/fundamentals/manage-members/roles/), [CF Zero Trust Roles](https://developers.cloudflare.com/cloudflare-one/roles-permissions/), [CF Access OTP](https://developers.cloudflare.com/cloudflare-one/identity/one-time-pin/), [CF Access Session Management](https://developers.cloudflare.com/cloudflare-one/identity/users/session-management/), [CF Pages Custom Domains](https://developers.cloudflare.com/pages/configuration/custom-domains/), [CF MCP Server](https://github.com/cloudflare/mcp), [CF Access App Paths](https://developers.cloudflare.com/cloudflare-one/access-controls/policies/app-paths/), [CF Access Custom Pages](https://developers.cloudflare.com/cloudflare-one/reusable-components/custom-pages/access-block-page/), [VitePress](https://vitepress.dev/), [VitePress Search](https://vitepress.dev/reference/default-theme-search), [vitepress-plugin-mermaid](https://github.com/emersonbottero/vitepress-plugin-mermaid), [Typesense](https://typesense.org/), [vitepress-plugin-typesense](https://www.npmjs.com/package/vitepress-plugin-typesense), [Obsidian Publish](https://obsidian.md/publish)
- **Related issues:** [#1349 — New Company Formation](https://github.com/DaveX2001/deliverable-tracking/issues/1349)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

