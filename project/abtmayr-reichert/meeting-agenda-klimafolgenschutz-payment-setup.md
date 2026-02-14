---
publish: true
---

# Meetingagenda: Klimafolgenschutz — Zahlungseinrichtung & Vereins-Voraussetzungen
[[client-abtmayr-reichert]]

## Meetingziel

Zuerst das Design Doc gemeinsam durchgehen — sieht Michael etwas, das ihm nicht gefällt oder fehlt? Danach die offenen Punkte klären, damit wir mit der Umsetzung starten können.

1. **Design Doc Review** — gemeinsam durchgehen, Feedback und Korrekturen
2. **Vereinsstatus + Zahlungs-Voraussetzungen** — offene Punkte klären (Punkte 1-5)
3. **Projektumfang abstimmen** — Phasen, Aufwand und Meilensteine (Punkt 6)
4. **Offene Zulieferungen + Ideen** — was Michael liefert und was er sich zusätzlich vorstellt (Punkte 7-8)

## Vorab-Lektüre

- [Design Doc: Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website) — insbesondere Teil 3 (Zahlung), aktualisiert am 12. Feb 2026
- [Aufwandsschätzung: Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/scoping-estimate-klimafolgenschutz-website) — Richtpreisangebot mit Phasen-Meilensteinen

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

### 5. Stripe-Onboarding: Dokumente und Daten von Michael

Für die Zahlungsaktivierung braucht Stripe folgende Unterlagen. Auch wenn nicht alles sofort verfügbar ist — Michael sollte wissen, was zu sammeln ist.

| Dokument / Information | Status |
|------------------------|--------|
| Vereinsregister-Auszug (< 3 Monate alt) | ⏳ Blockiert — Eintrag ausstehend |
| Vereinssatzung | ✅ Vorhanden |
| Steuernummer | ❓ Bestätigung ausstehend |
| Kleinunternehmer-Status (§19 UStG) | ❓ Steuerberater klären (→ Punkt 3) |
| IBAN (Vereinskonto) | ⏳ Blockiert — erst nach Vereinsregister |
| Ausweis Vorstandsmitglied | ✅ Verfügbar |
| Gläubiger-ID (für SEPA-Lastschrift) | ⏳ Blockiert — erst nach Bankkonto |

**Stripe-Konditionen (Überblick):**
- Keine monatliche Grundgebühr — Zahlung nur bei Nutzung
- Auszahlung auf Vereinskonto: 3-5 Werktage
- Chargeback-Gebühr: €15 pro Fall
- Jederzeit kündbar, keine Vertragsbindung
- DSGVO-konform (Stripe EU/Irland)
- Vollständige Bedingungen: [Stripe Services Agreement](https://stripe.com/de/legal/ssa) · [Preise](https://stripe.com/de/pricing) · [SEPA-Lastschrift](https://stripe.com/de/legal/sepa-direct-debit)

| Zahlungsart | Anwendungsfall | Beispielgebühr |
|-------------|---------------|----------------|
| SEPA-Lastschrift | Einzelmitgliedschaft (wiederkehrend) | ~0,8% + €0,25 (max €6) |
| Stripe Invoicing | Gemeinde-Beiträge (per Überweisung) | ~0,4% pro Rechnung |
| Karte (Visa/MC) | Einmalspenden | ~1,5% + €0,25 |

_Gebühren sind Richtwerte basierend auf aktueller Stripe-Preisliste._

- **Benötigt:** Michael prüft, welche Punkte er jetzt schon vorbereiten kann, und liest die Stripe-Konditionen

### 6. Projektumfang in fünf Phasen mit Meilenstein-Preisen

Die Website ist in fünf aufeinander aufbauende Phasen gegliedert. Jede Phase liefert ein eigenständiges Ergebnis. Abrechnung erfolgt nach tatsächlichem Aufwand (T&M, €120/Std.).

| Phase | Lieferergebnis | ~Stunden | Kumuliert (€120/Std.) |
|-------|---------------|----------|----------------------|
| 1 | Technische Basis + Design | ~3-5 | ~€360-600 |
| 2 | Inhaltsseiten + Mitgliedschaftsformulare | ~6-10 | ~€1.080-1.800 |
| 3 | Zahlungsintegration (Stripe) | ~4-6 | ~€1.560-2.520 |
| 4 | Zertifizierung | ~2-3 | ~€1.800-2.880 |
| 5 | CMS — eigenständige Inhaltspflege | ~4-6 | ~€2.280-3.600 |

- Phase 3 ist abhängig vom Vereinsregistereintrag (aktuell blockiert)
- Phase 4 setzt voraus, dass Michael die Zertifizierungskriterien liefert
- Phase 5 ist optional — ohne CMS werden Inhaltsänderungen vom Entwickler umgesetzt
- Richtpreisangebot innerhalb des bestehenden Rahmenvertrags

### 7. Zusätzliche Ideen von Michael

Michael hat zwei Erweiterungen vorgeschlagen, die nicht im aktuellen Umfang enthalten sind:

- **Interaktive Bedarfsanalyse-Seite** — Gemeinden prüfen vorhandene Infrastruktur (Hitzeschutz, Digitalisierung, Sensorik) über einen Fragebogen, der in den Mitgliedschafts-Flow mündet
- **Spielerisches Engagement-Element ("Moorhuhn")** — Gamification-Ansatz auf der Website

Beide Ideen brauchen eine Beschreibung dessen, was Michael sich konkret vorstellt, bevor eine Aufwandsschätzung möglich ist.

### 8. Offene Zulieferungen von Michael

Folgende Punkte werden von Michael benötigt, um mit der Umsetzung beginnen zu können:

| Zulieferung | Wofür | Blockiert Phase |
|-------------|-------|-----------------|
| Ionos DNS-Zugangsdaten | Domain klimafolgenschutz.com auf Server zeigen | Phase 1 |
| Logo-Dateien | Branding, Header, Favicon | Phase 1 |
| Impressum über [e-Recht24](https://www.e-recht24.de/) ausfüllen | Impressum + Datenschutzerklärung | Phase 2 |
| Gemeinde-Preisstufen bestätigen (Staffelung nach Einwohnerzahl) | Mitgliedschaftsformular + Preisanzeige | Phase 2 |
| Zertifizierungskriterien für "klimaresiliente Gemeinde" | Zertifizierungs-Sektion | Phase 4 |
| GenSpark Prototyp-Link per E-Mail senden | Design-Referenz | Phase 1 |

- Keine dieser Zulieferungen blockiert den Projektstart — Platzhalter werden eingesetzt
- **Benötigt:** Michael bestätigt, was er bis wann liefern kann

## Meetingformat

- **Typ:** Review (45-60 Min)
- **Vorbereitung:** Michael prüft Punkte 2-3 vorab mit Steuerberater falls möglich
- **Ergebnis:** Aktualisiertes Design Doc (Zahlungspunkte Defined) + abgestimmter Projektumfang für Umsetzungsstart

## Verknüpfungen

- **Issue:** [#726 — Michael Reichert Climate Association Website Development](https://github.com/DaveX2001/deliverable-tracking/issues/726)
- **Design Doc:** [Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website)
