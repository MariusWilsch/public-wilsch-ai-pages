---
publish: true
---

# REKERS Evaluierungsliste — Validierungsdesign Besprechung
[[client-rekers]]

## Besprechungsziel

Validierungsdesign für den V006 Machbarkeitsbericht klären, damit die POC-Tests mit einer gemeinsamen Definition von "richtigen Ergebnissen" starten können.

1. **Evaluierungsliste abgestimmt** — Einigung auf Testanfragen, erwartete Treffer, und CSV-Vorlage (4 Spalten)
2. **Umgang mit "nichts gefunden" geklärt** — Einigung auf Testfälle, bei denen kein Treffer die richtige Antwort ist
3. **Eingangsdefinition abgestimmt** — Einigung auf was eine neue Anfrage bei Ankunft enthält (Auslöser für das Abgleichsystem)

## Vorablektüre

- [Entwurfsdokument — Machbarkeitsbericht](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report) (Teil 4: Validierungsdesign)
- [Datenauswertung — Testdaten](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)

---

## Diskussionspunkte

*Einstiegspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Testanfragen aus den 14 gelieferten Projekten

Ansatz: 10 Projekte als Referenz, die 11. als Testanfrage — findet das System die richtigen ähnlichen Projekte?

- Wir brauchen vorab die erwarteten Treffer pro Testanfrage
- Konkret: "Für Testanfrage X erwarten wir Projekt Y — weil Gebäudetyp und Höhe übereinstimmen"
- CSV-Vorlage mit 4 Spalten steht bereit: Testanfrage, Erwarteter Treffer, Fehlfährte (ja/nein), Begründung
- Ausschlussverfahren (Leave-one-out): 9 Projekte als Datenbestand, das 10. abfragen, Ergebnis mit Erwartung vergleichen

### 2. "Nichts gefunden" als gültiges Ergebnis

Nicht jede Anfrage hat einen Treffer. Das System muss auch sagen können: "Dafür haben wir kein vergleichbares Projekt."

- Wir brauchen Testfälle, bei denen kein Treffer die richtige Antwort ist
- Z.B. ein Gebäudetyp den wir noch nie gebaut haben, oder ein Kunde ohne Referenzhistorie
- Ob diese aus den bestehenden 14 Projekten kommen oder als neue Szenarien konstruiert werden, ist offen

### 3. Der Anfrageprozess — vom Kundenkontakt zur Anfrage im System

Wir wissen wie die historischen Projekte aussehen (14 geliefert), aber nicht wie eine neue Anfrage bei Eingang aussieht. Das System braucht einen definierten Startpunkt: Was liegt vor, wenn ein neues Projekt hereinkommt? E-Mail vom Architekten mit Plänen? Anruf vom Bauherrn? Strukturiertes Formular?

- Welche Informationen sind bei der ersten Kontaktaufnahme typischerweise verfügbar?
- Unterscheiden sich die Anfragen je nach Kontaktweg (Architekt vs. Bauherr vs. Generalunternehmer)?
- Welche der 9 Ähnlichkeitskriterien können wir realistisch aus der Erstanfrage ableiten?

### 4. Zweck der "Angebote ohne Material" CSV

Die Datenlieferung enthielt eine CSV mit 4.307 Angebotszeilen (141 Spalten), die im Workshop nicht besprochen wurde. Unsere Analyse zeigt: die CSV enthält keine zusätzlichen Daten für den Ähnlichkeitsabgleich über die Anfragen.csv hinaus (Krankosten = universelle Montagekosten für Fertigteile, Materialgewichte = REKERS-Standardprodukt). War diese CSV für einen anderen Zweck gedacht?

## Besprechungsformat

- **Typ:** Arbeitssitzung
- **Teilnehmer:** Herr Sasse + Marius (ggf. Kalkulator)
- **Erwartung:** Herr Sasse kommt mit Kenntnis der 14 gelieferten Projekte und einer Einschätzung, welche davon "ähnlich" zueinander sind
- **Ergebnis:** Gemeinsames Verständnis der Evaluierungsmethode und Einigung auf den Inhalt der Evaluierungsliste. Die CSV wird anschließend von REKERS ausgefüllt.

## Verweise

- **Vorgang:** [#629 — REKERS: KI-gestütztes Angebotssystem - Workshop 2](https://github.com/DaveX2001/deliverable-tracking/issues/629)
- **Entwurfsdokument:** [Machbarkeitsbericht](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Datenauswertung:** [Testdaten-Bericht](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)
