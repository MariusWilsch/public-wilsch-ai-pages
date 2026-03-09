---
publish: true
---

# Meeting Agenda: Client Sync — Klimafolgenschutz Website Abschluss

[[project-abtmayr-reichert]]

**Date:** Wednesday, 2026-03-12
**Attendees:** Marius Wilsch, Michael Reichert

## Meeting Goal

Two blockers that prevent the Klimafolgenschutz website from completion. Everything else is built and live.

1. **Stripe Payment prerequisites clarified** — Vereinsregister status, blocker chain visibility, and expected timeline so we can plan the payment integration work
2. **Zertifizierung content captured** — Michael explains the certification criteria and process for "klimaresiliente Gemeinde" verbally, we write the website copy from his explanation

## Pre-Read

- [Live Website — klimafolgenschutz.org](https://klimafolgenschutz.org) — show to Michael for pricing sign-off
- [Investigation Findings (Issue #1084 comment)](https://github.com/DaveX2001/deliverable-tracking/issues/1084) — full project status audit
- [Design Doc — Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website) — original scoping

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Stripe Payment Integration — Voraussetzungskette
⏱️ 15 min

Die Zahlungsintegration (#762) ist das letzte große Feature. Die Website ist live, alle anderen Sections sind fertig. Wir sind blockiert durch eine Kette von Voraussetzungen, die nur Michael auslösen kann.

- **Blocker-Kette:** Vereinsregister → Bankkonto → IBAN → Gläubiger-ID (ca. 10 Tage) → Steuernummer
- Im Februar-Meeting hieß es "1-2 Wochen" für den Vereinsregister — jetzt sind 3+ Wochen vergangen
- Ohne diese Kette können wir Stripe nicht konfigurieren (SEPA, Karte, Invoicing)
- **Kleinunternehmerregelung:** Die ~€500/Jahr Gemeinde-Beiträge werden als Rechnung gestellt. Der Verein startet als Kleinunternehmer — keine MwSt auf Rechnungen. Sobald die Steuernummer da ist: mit Steuerberater bestätigen
- **Downstream:** Impressum + Datenschutz brauchen ebenfalls die Vereinsregister-Adresse und VR-Nummer (für e-Recht24 Generatoren)

**To resolve:** Status der gesamten Voraussetzungskette — wo steht der Vereinsregister-Eintrag, und was können wir als Zeithorizont erwarten. Wir sind blockiert, solange diese Schritte nicht abgeschlossen sind.

### 2. Zertifizierung "Klimaresiliente Gemeinde" — Inhalte
⏱️ 10 min

Die Zertifizierungs-Section (#763) ist technisch fertig und ans CMS angebunden. Was fehlt: der Inhalt. Im Februar-Meeting wurde vereinbart, dass Michael die Kriterien und den Ablauf liefert.

- Was genau macht eine Gemeinde zur "klimaresilienten Gemeinde"?
- Wie läuft der Zertifizierungsprozess Schritt für Schritt ab?
- Wie stark soll das auf der Website abgekürzt werden?
- Geschätzter Aufwand für uns: 2-3 Stunden, sobald der Inhalt steht

**To resolve:** Michael erklärt Kriterien und Ablauf der Zertifizierung mündlich — wir schreiben den Website-Text daraus.

### 3. Falls Zeit: IONOS DNS-Zugangsdaten
⏱️ 3 min

klimafolgenschutz.com zeigt noch die alte IONOS-Seite (217.160.0.187) statt auf klimafolgenschutz.org weiterzuleiten. Für die Umleitung brauchen wir IONOS-Login-Daten von Michael.

**To resolve:** IONOS-Zugangsdaten von Michael erhalten, damit .com auf .org weiterleitet.

### 4. Falls Zeit: Mitgliedschafts-Preise bestätigen
⏱️ 3 min

Die Preisstufen für Gemeinde-Mitgliedschaften sind im CMS hinterlegt und auf der Website sichtbar. Michael sieht die Preise auf dem geteilten Bildschirm und bestätigt.

**To resolve:** Visuelle Bestätigung der angezeigten Mitgliedschafts-Preise auf der Live-Website.

## Meeting Format

- **Type:** Discovery
- **Duration:** ~30 min
- **Attendees:** Marius Wilsch, Michael Reichert
- **Expectation:** Michael braucht keine Vorbereitung — Fragen werden im Meeting erklärt
- **Outcome:** Klarheit über Vereinsregister-Timeline und Zertifizierungsinhalt, damit die letzten zwei Features umgesetzt werden können

## Related

- **Issue:** [#1084 — Meeting Agenda Client Sync 2026-03-12](https://github.com/DaveX2001/deliverable-tracking/issues/1084)
- **Epic:** [#726 — Klimafolgenschutz Website](https://github.com/DaveX2001/deliverable-tracking/issues/726)
- **Blocked Issues:** [#762 — Stripe Payment](https://github.com/DaveX2001/deliverable-tracking/issues/762) | [#763 — Zertifizierung](https://github.com/DaveX2001/deliverable-tracking/issues/763)
- **Design Doc:** [Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website)
- **Prior Meeting Agenda:** [Payment Setup (Feb 12)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/meeting-agenda-klimafolgenschutz-payment-setup)
