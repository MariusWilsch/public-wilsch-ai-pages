---
publish: true
---

# Meetingagenda: Klimafolgenschutz — Zahlungseinrichtung & Vereins-Voraussetzungen
[[client-abtmayr-reichert]]

## Meetingziel

Zuerst das Design Doc gemeinsam durchgehen — gibt es etwas, das nicht gefällt oder fehlt? Danach die offenen Punkte klären, damit wir mit der Umsetzung starten können.

1. **Design Doc Review** — gemeinsam durchgehen, Feedback und Korrekturen
2. **Vereinsstatus + Zahlungs-Voraussetzungen** — offene Punkte klären (Punkte 1-4)
3. **Projektumfang abstimmen** — Phasen, Aufwand und Meilensteine (Punkt 5)
4. **Offene Zulieferungen + Ideen** — was Sie liefern und was Sie sich zusätzlich vorstellen (Punkte 6-7)

## Vorab-Lektüre

- [Design Doc: Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website) — insbesondere Teil 3 (Zahlung), aktualisiert am 12. Feb 2026
- [Aufwandsschätzung: Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/scoping-estimate-klimafolgenschutz-website) — Richtpreisangebot mit Phasen-Meilensteinen

---

## Diskussionspunkte

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Vereinsstatus — zwei offene Fragen

Zwei getrennte Vorgänge bestimmen, was die Website beim Launch anbieten kann:

**Vereinsregister (Amtsgericht):**
- Antrag seit Juli 2025, Stand 9. Feb: noch in Prüfung
- Ohne Eintrag: kein Bankkonto, keine Gläubiger-ID, kein Impressum, keine Zahlung
- Website kann trotzdem ohne Zahlung starten — Zahlungsaktivierung erfolgt separat
- **→ Wann rechnen Sie mit dem Eintrag?**

**Gemeinnützigkeit (Finanzamt):**
- Der Spendenbutton funktioniert in jedem Fall — Spenden sind immer möglich
- Ohne Freistellungsbescheid: kein "steuerlich absetzbar"-Hinweis auf der Website, keine Spendenbescheinigungen
- Mit Freistellungsbescheid: "steuerlich absetzbar" bewerben + Spendenbescheinigungen ab €100 ausstellen (Name + Adresse erforderlich)
- Das Dokument kommt vom Finanzamt (über Ihren Steuerberater)
- **→ Haben Sie einen Freistellungsbescheid, ja oder nein? Falls nein: ist er beantragt?**

### 2. Stripe-Setup: Kleinunternehmer oder umsatzsteuerpflichtig?

- Stripe fragt bei der Einrichtung nach §19 UStG Kleinunternehmerregelung
- Bestimmt, ob Rechnungen an Gemeinden mit oder ohne Umsatzsteuer gestellt werden
- **→ Was sagt Ihr Steuerberater: Kleinunternehmer oder regelbesteuert?**

### 3. Spendenbescheinigung — Daten vor oder nach der Zahlung?

- Stripe Checkout erfasst nur E-Mail + Zahlungsdaten
- Für eine Spendenbescheinigung (≥€100) brauchen wir vollständigen Namen + Adresse
- Option A: Felder direkt vor der Zahlung abfragen
- Option B: Per E-Mail nachfassen
- **→ Welche Variante bevorzugen Sie?**

### 4. Stripe-Onboarding: Dokumente und Daten

Für die Zahlungsaktivierung braucht Stripe folgende Unterlagen. Auch wenn nicht alles sofort verfügbar ist — hier eine Übersicht, was zu sammeln ist.

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

- **Benötigt:** Prüfen Sie, welche Punkte Sie jetzt schon vorbereiten können, und lesen Sie die Stripe-Konditionen

### 5. Projektumfang in fünf Phasen mit Meilenstein-Preisen

Die Website ist in fünf aufeinander aufbauende Phasen gegliedert. Jede Phase liefert ein eigenständiges Ergebnis. Abrechnung erfolgt nach tatsächlichem Aufwand (T&M, €120/Std.).

| Phase | Lieferergebnis | ~Stunden | Kumuliert (€120/Std.) |
|-------|---------------|----------|----------------------|
| 1 | Technische Basis + Design | ~3-5 | ~€360-600 |
| 2 | Inhaltsseiten + Mitgliedschaftsformulare | ~6-10 | ~€1.080-1.800 |
| 3 | Zahlungsintegration (Stripe) | ~4-6 | ~€1.560-2.520 |
| 4 | Zertifizierung | ~2-3 | ~€1.800-2.880 |
| 5 | CMS — eigenständige Inhaltspflege | ~4-6 | ~€2.280-3.600 |

- Phase 3 ist abhängig vom Vereinsregistereintrag (aktuell blockiert)
- Phase 4 setzt voraus, dass Sie die Zertifizierungskriterien liefern
- Phase 5 ist optional — ohne CMS werden Inhaltsänderungen vom Entwickler umgesetzt
- Richtpreisangebot innerhalb des bestehenden Rahmenvertrags

### 6. Ihre zusätzlichen Ideen

Sie haben zwei Erweiterungen vorgeschlagen, die nicht im aktuellen Umfang enthalten sind:

- **Interaktive Bedarfsanalyse-Seite** — Gemeinden prüfen vorhandene Infrastruktur (Hitzeschutz, Digitalisierung, Sensorik) über einen Fragebogen, der in den Mitgliedschafts-Flow mündet
- **Spielerisches Engagement-Element ("Moorhuhn")** — Gamification-Ansatz auf der Website

Beide Ideen brauchen eine Beschreibung dessen, was Sie sich konkret vorstellen, bevor eine Aufwandsschätzung möglich ist.

### 7. Offene Zulieferungen von Ihnen

Folgende Punkte werden von Ihnen benötigt, um mit der Umsetzung beginnen zu können:

| Zulieferung | Wofür | Blockiert Phase |
|-------------|-------|-----------------|
| Ionos DNS-Zugangsdaten | Domain klimafolgenschutz.com auf Server zeigen | Phase 1 |
| Logo-Dateien | Branding, Header, Favicon | Phase 1 |
| Impressum über [e-Recht24](https://www.e-recht24.de/) ausfüllen | Impressum + Datenschutzerklärung | Phase 2 |
| Gemeinde-Preisstufen bestätigen (Staffelung nach Einwohnerzahl) | Mitgliedschaftsformular + Preisanzeige | Phase 2 |
| Zertifizierungskriterien für "klimaresiliente Gemeinde" | Zertifizierungs-Sektion | Phase 4 |
| GenSpark Prototyp-Link per E-Mail senden | Design-Referenz | Phase 1 |

- Keine dieser Zulieferungen blockiert den Projektstart — Platzhalter werden eingesetzt
- **Benötigt:** Bestätigen Sie, was Sie bis wann liefern können

## Meetingformat

- **Typ:** Review (45-60 Min)
- **Vorbereitung:** Punkt 1 (Gemeinnützigkeit) und Punkt 2 (Kleinunternehmer) vorab mit Steuerberater prüfen falls möglich
- **Ergebnis:** Aktualisiertes Design Doc (Zahlungspunkte Defined) + abgestimmter Projektumfang für Umsetzungsstart

## Verknüpfungen

- **Issue:** [#726 — Michael Reichert Climate Association Website Development](https://github.com/DaveX2001/deliverable-tracking/issues/726)
- **Design Doc:** [Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website)
