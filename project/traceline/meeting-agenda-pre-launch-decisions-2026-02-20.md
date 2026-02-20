---
publish: true
---

# Beta Launch — Pre-Launch Decisions
[[traceline]]

## Meeting Goal

Lock the remaining decisions for Feb 23 launch. Near-term items are launch-blocking; long-term items shape post-beta trajectory but don't block Monday.

1. **Positioning name locked** — concept is articulated, name isn't (README needs it)
2. **Feedback notification stance** — close the loop or leave open for beta?
3. **Beta check-in cadence** — structured feedback meetings with first 2 users
4. **Data collection & business model direction** — long-term, not launch-blocking, but shapes messaging
5. **AI behavior testing approach** — long-term, operational concern for ongoing releases

## Pre-Read

- [Developer Position Beta — Plugin Scope & Delivery](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope) (design doc, updated 2026-02-20)
- [Developer Operations Manual v3](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The concept is clear but the name isn't locked for the README
⏱️ 15 min

"AI doesn't need more tech people. It needs organizational people." The system treats AI like an employee — position agreements, training, standards, accountability. Developer position makes AI-assisted development deterministic (Issues → Code). Problem frame: rogue developer → organizational standards. "Traceline" has reservations from Christoph ("es verkörpert nicht was wir tun").

- "Assistant" has no differentiation, "interaction patterns" too academic, "behavioral scripts" too clinical
- The actual value: consistency and the ability to improve when things go wrong
- Each position = a product with clear input/output boundaries (JA: Transcripts → Issues, Developer: Issues → Code)
- Reference context: [Sourcegraph — Revenge of the Junior Developer](https://sourcegraph.com/blog/revenge-of-the-junior-developer)

**To resolve:** A name or descriptor that conveys "organizational standards for AI" without being boring or too meta — needed for the README vision section before launch.

### 2. Users submit feedback but never hear back
⏱️ 5 min

`/feedback` routes to CCI directly. Users see a confirmation but not how feedback is triaged or resolved. For beta with 2 users, the Discord channel provides a personal communication layer. At 10 users, the absence of a feedback loop could feel like a black hole.

- Options: no notification (trust-based), automated "fixed in release X", manual follow-up via Discord
- Cost vs. signal: automation adds engineering, manual doesn't scale

**To resolve:** Whether feedback resolution notifications are a beta feature or a post-beta improvement.

### 3. Structured check-ins with beta users could compound the improvement loop
⏱️ 10 min

At Lawable, weekly "what's working well / what's working bad" meetings with early users surfaced issues faster than async feedback. Transcripts from these check-ins feed back into design doc improvement — the same extraction pass mechanism used internally.

- First 2 users (David, Dylan) are close enough for 1:1 check-ins
- At 10 users: group session or individual?
- Cadence: weekly, biweekly, or on-demand?
- Transcripts from check-ins become JA source material

**To resolve:** The format and cadence of structured feedback meetings for beta users — starting with the first 2.

### 4. Conversation data is the improvement fuel but consent isn't defined
⏱️ 10 min

The system improves by learning from user sessions — conversation data is how the operations manual evolves. Two monetization paths emerged: subscription to curated best practices (we improve via your data, "Michael Burry model") vs. self-serve (user keeps data private, less improvement).

- IP sensitivity is real: cybersecurity client's data cannot be in a shared space
- The right to collect/use conversation data could differentiate pricing tiers
- Beta users are trusted collaborators — consent may be simpler now, but needs a model for scale
- "Works towards your way of working, not our way of thinking — while you can subscribe to ours"

**To resolve:** The data collection consent mechanism for beta and the directional decision on whether data usage rights become a monetization lever.

### 5. The system tests itself internally — but not on the client's machine
⏱️ 5 min

AI behavior is validated by using the system's own workflow (clarity phases, AC verify, DoD tracking) on real work — not by a separate test suite. The open question is client-side: Marius's alpha environment is the test bed, but beta users have different setups (different OS, different shells, different Claude Code versions, different project structures).

- Internal validation works because the improved system workflow is used daily on real work
- Risk: environment differences (not behavioral differences) cause failures on client machines
- First 2 users (David, Dylan) are the canary — their setup issues are the test

**To resolve:** Whether the first-2-user observation period is sufficient client-side verification, or if additional environment checks are needed in the install script.

## Meeting Format

- **Type:** Working session
- **Expectation:** Christoph has read the updated design doc
- **Outcome:** Topics 1-3 resolved before launch. Topics 4-5 directionally agreed, full resolution post-launch.

## Related

- **Issue:** [CCI#504: Cloud Code Operating Launch](https://github.com/DaveX2001/claude-code-improvements/issues/504)
- **Design Doc:** [Developer Position Beta — Plugin Scope & Delivery](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope)
- **Previous Agendas:**
  - [Final Scope Decisions (2026-02-17)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/meeting-agenda-final-scope-decisions-2026-02-17)
  - [Beta Launch Scope (2026-02-16)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/meeting-agenda-beta-launch-scope-2026-02-16)
