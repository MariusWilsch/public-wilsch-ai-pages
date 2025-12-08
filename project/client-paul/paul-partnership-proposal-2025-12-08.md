---
publish: true
---

# Paul Partnership Proposal
[[client-paul]]

## 1. Current State

Paul's system today:
- 3 frontends, 2 backends, single-tenant architecture
- Quality inconsistent, codebase messy
- Works for Paul's channels, not scalable for external clients

## 2. Target State

**Phase 1 - Validation (This week)**
- Deploy Paul's tar on my GPU box (prove code works elsewhere)
- Replace TTS + Whisper with parallel TTS
- Validate cost, availability, reliability
- Eric onboard as first client

**Phase 2 - Multi-tenant (Dec 15-31)**
- Consolidate to 1 frontend, 1 backend
- Proper database with multi-tenancy
- Authentication and data separation
- Containerization for staging/prod/dev per client

## 3. Architecture

| Layer | Runs on | Scales by |
|-------|---------|-----------|
| Orchestration | CPU box | Adding CPU boxes (cheap) |
| GPU workloads | RunPod serverless | Workers per endpoint |

## 4. Team

| Person | Role |
|--------|------|
| Marius | Technical lead - infra, scaling, architecture |
| Roland | Client-facing - sales, onboarding, support |
| Tech support | Ramping up |

## 5. Proposal Terms

- **60/40 split** on net profit (deduct infra first, split remainder)
- **Quarterly reevaluation** - mutual protection, revisit if contributions change
- **Lifetime platform access** - I host the infrastructure
- **Roland mentorship from Paul** - weekly calls, evaluated quarterly (non-negotiable)
