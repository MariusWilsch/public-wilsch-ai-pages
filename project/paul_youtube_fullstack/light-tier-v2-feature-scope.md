# Light Tier v2 Feature Scope
[[client-paul]]

---
publish: true
date: 2025-12-28
source: Dec 27 Paul-Marius call transcript
issue: "#353"
---

## Overview

**Light Tier** = Simplified, sellable version of Paul for small users (1 channel, 1 video/day).

**Architecture:**
- v2 domain with new frontend (Strangler Pattern)
- Supabase Auth + FastAPI backend
- v1 (existing "big boys") and v2 (Light Tier) coexist
- Migrate page-by-page, not big bang rewrite

**Key Decision:** New frontend via Strangler Pattern (not feature flags in same codebase).

---

## Navigation Items

| Sub-feature | v2 Light Tier | Notes |
|-------------|---------------|-------|
| Script Writer | ❌ OUT | Big boys only |
| Config | ⏸️ DEFER | Admin only, revisit later |
| Thumbnail Manager | ❌ OUT | Internal graphic designers |
| Create Video Template | ❌ OUT | Predefined templates only |
| Create Voice | ❌ OUT | Predefined voices only, no cloning |
| Channels (from Niches) | ✅ IN | Rename + build |
| Competition | ✅ IN | Tracking + analysis |
| Queue | ✅ IN | Simplified view |
| Script Checker | ✅ IN | Free feature |
| Master Prompt Builder | ✅ IN | Core feature |

---

## Channels Feature (formerly Niches)

| Sub-feature | v2 Light Tier | Notes |
|-------------|---------------|-------|
| Create channel | ✅ IN | Limit TBD (1 vs unlimited) |
| Edit channel | ✅ IN | Basic CRUD |
| Delete channel | ❓ TBD | Not discussed |
| YouTube OAuth + Upload | ❓ TBD | System load concern |
| Prompts config | ✅ IN | Optional |
| Workflow config | ✅ IN | Core |
| Preset voice selection | ✅ IN | Select only, no create |
| Speed setting | ✅ IN | Optional |
| Image style | ✅ IN | Predefined + custom |
| Skip image processing | ✅ IN | Toggle |
| Template assignment | ✅ IN | Predefined only |
| Update Statistics | ❌ OUT | Advanced |
| Manage Channels button | ✅ IN | Auth visibility |

---

## Competition Feature

| Sub-feature | v2 Light Tier | Notes |
|-------------|---------------|-------|
| Add competitor channels | ✅ IN | Core |
| Remove competitor channels | ✅ IN | Core |
| View competitor videos | ✅ IN | Analysis |
| Scrape competitor videos | ✅ IN | YouTube API |
| Filter/sort videos | ✅ IN | Core |

---

## Create Video Flow

| Sub-feature | v2 Light Tier | Notes |
|-------------|---------------|-------|
| Title input | ✅ IN | Core |
| Thumbnail Title input | ✅ IN | Core |
| Upload Script (file picker) | ✅ IN | Manual upload |
| "Use Completed Assigned Script" | ❌ OUT | Links to Script Writer |
| Video URL input | ✅ IN | Core |
| Title Generation toggle | ✅ IN | Core |
| Autopilot toggle | ✅ IN | Automation |
| Auto Upload YouTube | ✅ IN | Convenience |
| Enable Video Preview | ✅ IN | Question on GPU load |
| Require Thumbnail button | ✅ IN | Pricing TBD ($1-2) |
| Add CTA | ❌ OUT | Phase 2 |

---

## Queue View

| Sub-feature | v2 Light Tier | Notes |
|-------------|---------------|-------|
| View queue (own videos only) | ✅ IN | Core |
| View Final Video | ✅ IN | Core |
| Download Video | ✅ IN | Core |
| Thumbnail status indicator | ✅ IN | Shows when ready |
| Edit button (for failures) | ✅ IN | Retry failed jobs |
| Download Images | ❌ OUT | Simplified |
| Download Transcript | ❌ OUT | Simplified |
| Download Audio | ❌ OUT | Simplified |
| Edit Title | ❌ OUT | UUID migration |
| Delete | ❌ OUT | No delete |
| Redo | ❌ OUT | Advanced |
| Clone/Multi | ❌ OUT | Big boys |
| Generate Description | ❌ OUT | Advanced |
| Upload to YouTube (manual) | ❌ OUT | Use auto-upload |

---

## Voice System

| Sub-feature | v2 Light Tier | Notes |
|-------------|---------------|-------|
| View voices | ✅ IN | Select from list |
| Select voice | ✅ IN | ~16 predefined |
| Create voice | ❌ OUT | No custom |
| Clone voice | ❌ OUT | Security risk ("Trump stuff") |

---

## Template System

| Sub-feature | v2 Light Tier | Notes |
|-------------|---------------|-------|
| View templates | ✅ IN | Select from list |
| Select template | ✅ IN | Predefined only |
| Create template | ❌ OUT | Advanced |
| Edit template | ❌ OUT | Advanced |

---

## Infrastructure

| Sub-feature | v2 Light Tier | Notes |
|-------------|---------------|-------|
| v2 subdomain | ✅ REQUIRED | New domain |
| Supabase Auth | ✅ REQUIRED | User registration |
| Stripe integration | ✅ REQUIRED | Payments |
| Tier routing | ✅ REQUIRED | Light vs Full |
| Dark mode + Light mode | ✅ IN | UI polish |

---

## Question Marks (Need Decision)

| Item | Question | Notes |
|------|----------|-------|
| YouTube OAuth | System load concern - in or out? | Edge functions may help |
| Thumbnail pricing | Bundled or $1-2 extra? | Affects conversion |
| Channel limit | 1 channel or unlimited? | Paul said "as many as he wants" |
| Delete channel | Allowed or not? | Not discussed in call |
| Config page | What parts needed? | Deferred - revisit later |

---

## Summary Counts

| Category | IN | OUT | TBD |
|----------|-----|-----|-----|
| Navigation Items | 5 | 5 | 1 |
| Channels | 10 | 1 | 2 |
| Competition | 5 | 0 | 0 |
| Create Video Flow | 9 | 2 | 0 |
| Queue View | 5 | 9 | 0 |
| Voice System | 2 | 2 | 0 |
| Template System | 2 | 2 | 0 |
| Infrastructure | 5 | 0 | 0 |
| **TOTAL** | **43** | **21** | **3** |

---

## Implementation Approach

**JIT Vertical Slicing** (validated against Strangler Pattern)

### The Pattern

```
Start: Create Video UI (v2, Supabase)
       │
       ▼
   Empty dropdown? ──► Build Niche/Channel entity
       │
       ▼
   FK error (no voices)? ──► Seed Voice data
       │
       ▼
   Keep going until end-to-end works
       │
       ▼
   Move to next feature
```

### Why This Works

1. **Strangler Pattern recommends vertical slices by business domain**, not horizontal technical layers
2. "Extracting by technical layers (database → services → UI) is problematic" - industry consensus
3. Start with user-facing functionality, build dependencies as discovered
4. Scope doc = MAP (what entities exist), Errors = ORDER (when to build each)

### Known Dependencies

When building Create Video UI, these entities will be needed:

| Entity | UI Name | When Needed |
|--------|---------|-------------|
| Voice | Voice dropdown | Niche creation / Step 3 |
| ImageStyle | Image Style dropdown | Niche creation / Step 4 |
| VideoGenTemplate | Video Template dropdown | Niche creation |
| PromptConfig | Prompt configuration | Script generation |
| Template | Niche/Channel | Create Video Step 1 |

### Starting Point

**Create Video Flow** → discover → build → continue

---

## Source References

- **Transcript:** Dec 27, 2025 Paul-Marius call (Fireflies: 01KDG0H66T52T8BW7736QJRK83)
- **Migration Framework:** [supabase-migration-framework](./supabase-migration-framework.md)
- **Strangler Pattern ADR:** [adr-strangler-pattern-sequencing](./adr-strangler-pattern-sequencing.md)
- **Issue:** DaveX2001/deliverable-tracking#353
