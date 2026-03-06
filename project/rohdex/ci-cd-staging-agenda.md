---
publish: true
---

# Meeting Agenda: Staging Email Mailbox — Rohdex
[[project-rohdex]]

## Meeting Goal

Resolve the external dependency blocking full E2E staging: a second IONOS mailbox dedicated to the staging environment.

1. **Mailbox creation path** — who requests it, through which channel, and expected timeline
2. **Mailbox specification** — address format, IMAP/SMTP credentials handoff

## Pre-Read

- [CI/CD & Staging Design Doc](ci-cd-staging-design) — Part 1: Staging Email Isolation explains why a second mailbox is needed

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. IONOS mailbox creation for staging
⏱️ 10 min

The Dokumentenverarbeitung system moves emails out of INBOX after processing. Two instances polling the same mailbox means the first one to poll consumes the email. A dedicated staging mailbox eliminates this race condition entirely — the env vars already support pointing each environment at a different address.

- Wilsch AI does not have IONOS admin access — cannot create mailboxes
- During migration, Roman and Manuel Primke (Gmelch IT) handled IONOS email configuration
- Konstantin (Rohdex) has been the coordination point for infrastructure requests
- The staging mailbox only needs IMAP + SMTP access (same as production)

**To resolve:** The channel and contact for requesting a new IONOS mailbox, and expected turnaround time for provisioning.

## Meeting Format

- **Type:** Quick coordination (async or 5-min sync)
- **Expectation:** Marius confirms who to contact and initiates the request
- **Outcome:** Mailbox request submitted, expected delivery date known

## Related

- **Issue:** [#1067](https://github.com/DaveX2001/deliverable-tracking/issues/1067)
- **Design Doc:** [CI/CD & Staging Design](ci-cd-staging-design)
