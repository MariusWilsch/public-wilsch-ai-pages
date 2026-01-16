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
# Add variable to specific environment
vercel env add VITE_API_URL preview

# Add variable scoped to specific branch
vercel env add VITE_API_URL preview --git-branch staging

# Pull variables locally
vercel env pull .env.local
```

**Important:** Changes only apply to new deployments. Redeploy after changing variables.

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

## Best Practices

1. **Branch Naming**: Use consistent names (`staging`, `develop`)
2. **Environment Variables**: Scope to correct environment + branch
3. **DNS**: Use CNAME (Vercel can update IPs without DNS changes)
4. **Redeploy**: Always redeploy after changing env vars
5. **Password Protection**: Use Vercel deployment protection for staging (Dashboard > Deployment Protection)

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
