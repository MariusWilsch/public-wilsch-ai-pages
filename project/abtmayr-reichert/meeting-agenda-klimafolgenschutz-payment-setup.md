---
publish: true
---

# Meetingagenda: Klimafolgenschutz — Zahlungseinrichtung & Vereins-Voraussetzungen
[[client-abtmayr-reichert]]

## Meetingziel

Die offenen Vereins-Voraussetzungen für die Zahlungsintegration klären und zwei offene Design-Entscheidungen abstimmen. Nach diesem Meeting sollte der gesamte Zahlungsbereich im Design Doc von Undefined auf Defined stehen.

1. **Vereinsstatus klären** — aktueller Stand von Vereinsregister, Gemeinnützigkeit und Bankkonto
2. **Zahlungs-Designentscheidungen** — Spendenquittungs-Logistik, PayPal-Risiko-Hinweis

## Vorab-Lektüre

- [Design Doc: Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website) — insbesondere Teil 3 (Zahlung), aktualisiert am 12. Feb 2026

---

## Diskussionspunkte

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Vereinsregistereintrag seit Juli 2025 ausstehend

- Stand 9. Feb: Eintrag noch in Prüfung
- Ohne Registrierung: kein Bankkonto, keine Gläubiger-ID, kein Impressum
- Website kann ohne Zahlung starten — Zahlungsaktivierung erfolgt separat
- **Benötigt:** Aktueller Zeitplan der Registrierung

### 2. Gemeinnützigkeitsstatus beeinflusst Spenden-Messaging

- Mit Freistellungsbescheid → Spenden ab €100 steuerlich absetzbar
- Ohne → Spendenbutton funktioniert trotzdem, aber keine Steuervorteile bewerben
- CMS-Textumschaltung, kein technischer Blocker
- **Benötigt:** Aktueller Status Gemeinnützigkeit

### 3. Stripe-Setup: Kleinunternehmer oder umsatzsteuerpflichtig?

- Stripe fragt §19 UStG Kleinunternehmerregelung ab
- Bestimmt: Rechnungen an Gemeinden mit/ohne USt
- **Benötigt:** Bestätigung vom Steuerberater

### 4. Spendenbescheinigung braucht Name + Adresse

- Stripe Checkout erfasst nur E-Mail + Zahlungsdaten
- Spendenbescheinigung (≥€100) erfordert vollständigen Namen + Adresse
- Optionen: Felder vor Zahlung abfragen ODER per E-Mail nachfassen
- **Benötigt:** Michaels Präferenz für den Checkout-Flow

### 5. PayPal-Spenden ohne Käufer-/Verkäuferschutz

- Angefochtene Spenden → PayPal entscheidet nicht zugunsten des Vereins
- Standard-Verhalten bei Spenden, geringes Risiko
- **Benötigt:** Kenntnisnahme vor PayPal-Aktivierung

## Meetingformat

- **Typ:** Review (30 Min)
- **Vorbereitung:** Michael prüft Punkte 2-3 vorab mit Steuerberater falls möglich
- **Ergebnis:** Aktualisiertes Design Doc — alle Zahlungspunkte Defined

## Verknüpfungen

- **Issue:** [#726 — Michael Reichert Climate Association Website Development](https://github.com/DaveX2001/deliverable-tracking/issues/726)
- **Design Doc:** [Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website)
