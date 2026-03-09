---
publish: true
[[docker-deployment]]
description: "Vercel staging environment patterns using branch-based deployments, custom domains, and environment variables"
---

# Vercel Staging Environment Patterns

## Overview

Vercel has three built-in environments: **Production**, **Preview**, and **Development**. There is no native "staging" concept. Staging workflows are implemented through branch-based configurations or custom environments (Pro plan).

## Vercel Environment Model

| Environment | Trigger | Purpose | System Variable |
|-------------|---------|---------|-----------------|
| **Production** | Push to default branch or `vercel --prod` | Live traffic | `VERCEL_ENV=production` |
| **Preview** | Push to non-default branch or `vercel` | Feature testing | `VERCEL_ENV=preview` |
| **Development** | `vercel dev` | Local dev | `VERCEL_ENV=development` |

## Staging Patterns

### Pattern 1: Branch-Based Staging (Hobby Plan)

**Setup:**
1. Create `staging` branch
2. Assign custom domain to staging branch
3. Configure branch-specific environment variables

```bash
# 1. Create staging branch
git checkout -b staging
git push -u origin staging

# 2. In Vercel Dashboard:
#    Project Settings > Domains > Add domain
#    staging.yourdomain.com → assign to "staging" branch

# 3. In Vercel Dashboard:
#    Project Settings > Environment Variables
#    Add VITE_API_URL, select "Preview" + "staging" branch
```

**Workflow:**
```
main (production) ← staging (staging.domain.com) ← feature branches (preview)
```

### Pattern 2: Custom Environment (Pro Plan)

```bash
# Create custom staging environment
vercel deploy --target=staging

# Or via API:
curl -X POST https://api.vercel.com/v9/projects/{projectId}/custom-environments \
  -H "Authorization: Bearer $VERCEL_TOKEN" \
  -d '{"slug": "staging"}'
```

### Pattern 3: Multiple Projects (Enterprise)

Separate Vercel projects for production and staging with independent configuration.

## Environment Variables

```bash
# Add variable to specific environment (interactive prompt for value)
vercel env add VITE_API_URL preview

# Add variable scoped to specific branch (pipe value to avoid prompt)
echo "https://staging-api.example.com" | vercel env add VITE_API_URL preview staging

# List all env vars
vercel env ls

# Pull variables locally
vercel env pull .env.local
```

**CLI Syntax:** `vercel env add <NAME> <environment> [branch]`
- Environment: `production`, `preview`, or `development`
- Branch: Optional, scopes to specific Git branch

**⚠️ Use `printf` not `echo`:** When piping values, `echo` adds a trailing newline which breaks string comparisons:
```bash
# ❌ BAD - adds newline, value becomes "true\n"
echo "true" | vercel env add MY_VAR preview staging

# ✅ GOOD - no trailing newline, value is "true"
printf "true" | vercel env add MY_VAR preview staging
```

**Important:** Changes only apply to new deployments. Trigger redeploy:
```bash
# Push empty commit to trigger redeploy
git commit --allow-empty -m "chore: trigger redeploy" && git push
```

## Stable Branch URLs (No Custom Domain Needed)

Vercel auto-generates **stable URLs** for each branch:

```
{project}-git-{branch}-{team}.vercel.app
```

Example: `v2-frontend-faceless-git-staging-mariuswilschs-projects.vercel.app`

This URL is stable - every push to that branch deploys to the same URL. No DNS configuration needed.

## Custom Domain Assignment

**Via Dashboard:**
1. Project Settings > Domains > Add Domain
2. Configure DNS (CNAME to `cname.vercel-dns.com`)
3. Edit domain → Select Git branch (e.g., `staging`)

**Via CLI:**
```bash
vercel alias <preview-url> staging.yourdomain.com
```

## DNS Configuration

```
Type: CNAME
Name: staging
Value: cname.vercel-dns.com
```

## Deployment Commands

```bash
# Deploy to preview (default)
vercel

# Deploy to production
vercel --prod

# Deploy to custom environment (Pro)
vercel deploy --target=staging
```

## Key Dashboard Settings

### Auto-assign Custom Production Domains

**Location:** Settings → Environments → Production

Controls whether production domains auto-update on push to production branch.

| Setting | Behavior |
|---------|----------|
| **Enabled** (default) | Push to prod branch → auto-assigns custom domains |
| **Disabled** | Push to prod branch → deploys but domains unchanged, manual promotion required |

**Use case:** Disable for controlled releases where you manually promote deployments.

### Deployment Protection

**Location:** Settings → Deployment Protection

| Setting | Effect |
|---------|--------|
| **Vercel Authentication** | Preview deployments require Vercel login |
| **Standard Protection** | Protects all except production custom domains |

To make staging publicly accessible: Toggle OFF "Vercel Authentication".

### Production Branch

**Location:** Settings → Environments → Production → Branch Tracking

Change which branch triggers production deployments. Default is `main`.

**Note:** No CLI command available - dashboard only.

## Best Practices

1. **Branch Naming**: Use consistent names (`staging`, `develop`)
2. **Environment Variables**: Scope to correct environment + branch
3. **DNS**: Use CNAME (Vercel can update IPs without DNS changes)
4. **Redeploy**: Always redeploy after changing env vars
5. **Deployment Protection**: Disable Vercel Authentication for public staging access

## Workflow Example

```bash
# 1. Develop on feature branch
git checkout -b feature-x
git push origin feature-x  # → Preview URL

# 2. Merge to staging
git checkout staging && git merge feature-x
git push origin staging  # → staging.domain.com

# 3. Test on staging, then merge to production
git checkout main && git merge staging
git push origin main  # → domain.com
```

## References

- [Vercel Environments](https://vercel.com/docs/deployments/environments)
- [Vercel Environment Variables](https://vercel.com/docs/environment-variables)
- [Staging Setup Guide](https://vercel.com/kb/guide/set-up-a-staging-environment-on-vercel)
