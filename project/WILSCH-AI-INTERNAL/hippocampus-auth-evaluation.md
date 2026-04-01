---
publish: true
---

# Hippocampus Publishing: Auth + Access Control Evaluation
[[hippocampus-auth]]

Evaluation of publishing alternatives for hippocampus — adding authentication and access control while preserving the push-to-deploy workflow.

---

## Problem Statement

All 334 published hippocampus documents are publicly accessible at `mariuswilsch.github.io/public-wilsch-ai-pages/`. There is no authentication. Anyone with the URL — or who discovers it via search — can read every published design doc, proposal, meeting agenda, and methodology document.

Design documents are sensitive IP even when shared with specific clients. Sending a proposal link to Stafford does not mean it should be readable by competitors, other clients, or the general public. The current workflow — grab a link, send it — works well for speed but provides zero access control.

The copyright footer (© 2026 + CC BY-NC-ND 4.0) added via CI protects expression under Berne Convention but does not control who can read the content. Copyright says "you can't copy this." Authentication says "you can't see this."

**Preconditions:**
- The hippocampus contains proprietary methodology, contract strategies, and client-specific proposals that constitute competitive advantage
- Clients currently receive links to published docs and read them in the browser
- The team (Marius, David, AI agents) uses published docs as working artifacts reviewed via Speechify
- The `publish: true` frontmatter gate controls what gets published, but all published content is equally public

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Published hippocampus documents are private by default, with controlled sharing for clients, while preserving the current push-to-deploy workflow |
| **Success** | The team publishes and reads docs without friction. Clients access shared docs with minimal friction. No published document is accessible to unauthorized visitors. |
| **Done test** | Can someone without credentials access any published doc via direct URL? If NO → done. Can Marius send a doc link to a client and they read it within 60 seconds? If YES → done. |

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

All four options below replace the final output (GitHub Pages) while preserving or simplifying the publish chain.

### Option A: Keep Jekyll → Cloudflare Pages + Access

**What changes:** Hosting moves from GitHub Pages to Cloudflare Pages. Cloudflare Access sits in front as an authentication layer. Jekyll stays. Content stays. Frontmatter stays.

**Auth model:** Cloudflare Access (Zero Trust) intercepts every request at the network edge. Two identity providers configured simultaneously:
- **Team:** Google OAuth — sign in with `@wilsch-ai.com` Google account. Seamless.
- **Clients:** Email OTP — client clicks link, enters their email, receives a 6-digit code, enters it. Session lasts 24 hours (configurable up to 1 month). No account creation needed.

Marius controls access by allowlisting specific email addresses or entire domains in the Cloudflare dashboard. Path-level access policies are supported (e.g., `/project/livebook/` could have different rules than `/global/`), but site-wide auth is sufficient for the current need.

**Push-to-deploy:** Connect the GitHub repo to Cloudflare Pages. Every push to `main` triggers a Jekyll build automatically. Preview deployments per branch. The two-workflow chain could simplify to one repo (Cloudflare builds directly from source), but the existing chain also works — Cloudflare Pages can build from the pages repo instead.

**Migration:**
- Add `Gemfile` if not already committed (Cloudflare requires it)
- Check `baseurl` in `_config.yml` — GitHub Pages project sites use `/public-wilsch-ai-pages`, Cloudflare uses `/`
- DNS must move to Cloudflare (required for Access to intercept traffic)
- Custom layout, Mermaid.js, copyright footer — all transfer unchanged (static output)

**Cost:** Free. Cloudflare Pages (unlimited bandwidth, 500 builds/month) + Access Zero Trust (up to 50 authenticated users). No per-user cost within the limit.

**Limitations:**
- 50-user seat limit on free tier — seats are consumed by unique email addresses, not concurrent sessions. Revoking access requires manual user removal.
- DNS must be on Cloudflare — non-negotiable for Access to work.
- Corporate email scanners may auto-consume OTP codes before the client can enter them (Cloudflare-acknowledged limitation).

**Effort:** ~1 hour setup. Zero content migration.

### Option B: Keep Jekyll → Netlify + Identity

**What changes:** Hosting moves from GitHub Pages to Netlify. Netlify Identity provides user management with invite-only access. Jekyll stays. Content stays.

**Auth model:** Netlify Identity — a built-in user management system:
- **Team:** Invite by email. Sign in via email/password or Google/GitHub OAuth.
- **Clients:** Invite by email. Client receives invitation, creates a lightweight account (email + password), accesses the site. No external service account needed.
- **Invite-only mode:** Self-registration disabled. Only people Marius explicitly invites can create accounts.

Site-wide password protection (single shared password, no user accounts) is also available but requires the Pro plan ($20/month). Identity with invite-only is free and provides per-user tracking.

**Push-to-deploy:** Connect the GitHub repo in the Netlify UI. Every push to `main` triggers a Jekyll build. Automatic preview deployments per PR. `netlify.toml` defines build command (`bundle exec jekyll build`) and publish directory (`_site`).

**Migration:**
- Add `netlify.toml` with build config (3 lines)
- Specify Ruby version via `.ruby-version` file or `RUBY_VERSION` env var
- Custom layout, Mermaid.js, copyright footer — all transfer unchanged
- Add `netlify-identity-widget` script to the layout for the login UI (one `<script>` tag in `default.html`)
- Configure redirect rules in `_redirects` file to gate content behind Identity

**Cost:** Free. Netlify Identity supports unlimited users on all plans at no per-user cost. Site-wide password protection (alternative to Identity) requires Pro at $20/member/month.

**Limitations:**
- Identity requires a client-side JavaScript widget (`netlify-identity-widget`) added to the layout — a small code change to `default.html`.
- Gating a pure static Jekyll site requires redirect rules (`_redirects` with role-based rules) that route unauthenticated visitors to a login page. This is a well-documented pattern but not zero-config.
- No per-path access control without writing JWT-based redirect rules — possible but manual setup.
- Clients must create an account (email + password) — slightly more friction than email OTP.

**Effort:** ~2 hours setup. Minimal content migration (add Identity widget to layout + redirect rules).

### Option C: VitePress → Cloudflare Pages + Access

**What changes:** Both the SSG and hosting change. Jekyll is replaced by VitePress (a modern markdown-native documentation framework). Hosting moves to Cloudflare Pages with Access for auth. Content markdown files transfer; templates are rewritten.

**Why VitePress over Jekyll:** VitePress is purpose-built for documentation sites. It provides built-in search, a polished default theme, faster builds (Vite-powered), and better developer experience. A team migrated 300+ pages in approximately one week. The main migration work is replacing Jekyll's Liquid templates with Vue components.

**Auth model:** Same as Option A — Cloudflare Access with Google OAuth (team) + email OTP (clients). The auth layer is platform-level, independent of the SSG.

**Push-to-deploy:** Same as Option A — Cloudflare Pages builds on push.

**Migration:**
- 334 markdown files transfer with frontmatter intact (VitePress reads YAML frontmatter natively)
- Replace `{% include %}` Liquid tags with Vue components in markdown
- Replace `{% raw %}...{% endraw %}` blocks with `::: v-pre` directives
- Remove `layout:` frontmatter field (VitePress handles layouts via config)
- Install `vitepress-plugin-mermaid` for diagram support (one npm install + 3 lines config)
- Rewrite `default.html` as a VitePress custom theme (Vue components instead of HTML + Liquid)
- URL structure: VitePress uses filenames for routing. If current Jekyll uses `permalink:` frontmatter, files need renaming to match.
- VitePress treats broken relative links as build errors — this will surface existing broken links that Jekyll silently ignores

**Cost:** Free (same Cloudflare stack as Option A). VitePress is open source.

**Limitations:**
- ~1 week migration effort — not zero like Options A/B
- Vue component knowledge helpful for theme customization (not required for basic docs)
- Build tooling changes from Ruby (Bundler) to Node (npm/pnpm)

**Effort:** ~1 week for migration + Cloudflare setup. Ongoing DX improvement over Jekyll.

### Option D: Self-Hosted (Caddy + OAuth Proxy) on Existing Infrastructure

**What changes:** Hosting moves from GitHub Pages to a VPS the team already controls (wilsch-ai or DGX Spark). Caddy serves the static site with oauth2-proxy handling authentication. Jekyll stays (or any SSG). Deployment via GitHub Actions → rsync.

**Why self-hosted:** The team already has SSH access to servers (wilsch-ai, rohdex, iitr-staging) and uses Docker. A static site with auth is a ~10-line Caddyfile. No per-user cost at any scale. No vendor dependency for something this critical.

**Auth model:** Two layers, fully under team control:
- **Team:** Google OAuth via oauth2-proxy. Sign in with `@wilsch-ai.com`. Restrict by email domain.
- **Clients:** Signed URLs — time-limited links generated by a script. Client clicks the link, accesses the doc directly. No login, no password, no account. The URL IS the credential. Expires after configurable period (24 hours, 7 days, etc.).

Signed URLs are the key differentiator: Marius generates a link for a specific doc with an expiry, sends it to the client. The client reads it. After expiry, the link stops working. No friction for the client, full control for Marius.

Alternative for clients: basic auth (simple username/password per project directory) — less elegant but simpler to implement.

**Push-to-deploy:** GitHub Actions workflow (~35 lines of YAML):
1. Push to `main` triggers the workflow
2. Jekyll builds `_site/`
3. `rsync` deploys to the VPS over SSH
4. Caddy serves the updated content immediately (no build step on the server)

The existing two-workflow frontmatter-scanning chain can be preserved — the final deployment target simply changes from GitHub Pages push to rsync.

**Migration:**
- Set up Caddy + oauth2-proxy via Docker Compose on an existing server
- Configure Google OAuth app in Google Cloud Console (one-time)
- Add GitHub Actions workflow for rsync deployment
- Custom layout, Mermaid.js, copyright footer — all transfer unchanged
- DNS: point a subdomain (e.g., `docs.wilsch-ai.com`) to the VPS

**Cost:** ~$0/month additional if using existing servers. No per-user cost at any scale. VPS cost only if new server needed (~$6/month).

**Limitations:**
- Operational ownership — the team owns uptime, TLS renewal (Caddy handles this automatically), and Docker updates (~30 min/month maintenance)
- Signed URL generation requires a small script (Python or Bash, ~20 lines) — not a managed dashboard
- No managed CDN edge caching (Caddy serves from a single location). For a small team + occasional client access, this is irrelevant
- Initial setup more complex than managed platforms (~4 hours first time)

**Effort:** ~4 hours initial setup. Zero content migration. Ongoing ~30 min/month maintenance.

### Comparison

| Dimension | A: CF Pages + Access | B: Netlify + Identity | C: VitePress + CF | D: Self-Hosted |
|-----------|---------------------|----------------------|-------------------|----------------|
| **Team auth** | Google OAuth | Google/GitHub OAuth | Google OAuth | Google OAuth |
| **Client sharing** | Email OTP (30s) | Invite + account creation | Email OTP (30s) | Signed URLs (zero friction) |
| **Push-to-deploy** | Git push → auto-build | Git push → auto-build | Git push → auto-build | Git push → rsync |
| **Jekyll stays** | Yes | Yes | No (VitePress) | Yes (or any SSG) |
| **Content migration** | Zero | Zero | ~1 week | Zero |
| **Setup effort** | ~1 hour | ~2 hours | ~1 week | ~4 hours |
| **Cost** | Free (≤50 users) | Free (unlimited users) | Free (≤50 users) | ~$0 (existing servers) |
| **Vendor dependency** | Cloudflare | Netlify | Cloudflare | None |
| **Path-level auth** | Yes (native) | Manual (JWT rules) | Yes (native) | Yes (Caddyfile) |
| **Ops burden** | Zero | Zero | Zero | ~30 min/month |
| **DNS requirement** | Must be on Cloudflare | Any DNS | Must be on Cloudflare | Any DNS |

**Undefined:** Which option to proceed with — Marius decides after reading this evaluation. → [Meeting Agenda: Hippocampus Auth Decision](hippocampus-auth-meeting-agenda)

**Undefined:** Custom domain strategy — does `mariuswilsch.github.io` become `docs.wilsch-ai.com` or something else? → [Meeting Agenda](hippocampus-auth-meeting-agenda)

**Undefined:** Whether to invest in SSG modernization (VitePress) now or keep Jekyll and modernize later → [Meeting Agenda](hippocampus-auth-meeting-agenda)

---

## Source

- **Issue:** [#1217 — Evaluate Vercel for Hippocampus Publishing](https://github.com/DaveX2001/deliverable-tracking/issues/1217)
- **Trigger:** [#847 Comment 35-36](https://github.com/DaveX2001/deliverable-tracking/issues/847#issuecomment-4098610248) — copyright/IP discussion surfaced that published artifacts have no access control
- **Session:** JA extraction pass 2026-04-01 (session `3aa18eda-a131-488b-ab55-3d802f295edd`)
- **Investigated repos:** `veloxforce/claude-user-configs` (hippocampus source), `MariusWilsch/public-wilsch-ai-pages` (pages output), `MariusWilsch/junior-architect-position-plugin` (JA deploy chain)
- **Platform docs:** [Cloudflare Pages Jekyll guide](https://developers.cloudflare.com/pages/framework-guides/deploy-a-jekyll-site/), [CF Access OTP](https://developers.cloudflare.com/cloudflare-one/integrations/identity-providers/one-time-pin/), [Netlify Identity](https://docs.netlify.com/manage/security/secure-access-to-sites/identity/), [Vercel Deployment Protection](https://vercel.com/docs/security/deployment-protection)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

