---
publish: true
---

# REKERS Evaluierungsliste — Validierungsdesign Besprechung
[[client-rekers]]

## Meeting Goal

Validierungsdesign für den V006 Feasibility Report klären, damit die POC-Tests mit einer gemeinsamen Definition von "richtigen Ergebnissen" starten können.

1. **Evaluierungsliste abgestimmt** — Einigung auf Testanfragen, erwartete Treffer, und CSV-Vorlage (4 Spalten)
2. **Umgang mit "nichts gefunden" geklärt** — Einigung auf Testfälle, bei denen kein Treffer die richtige Antwort ist

## Pre-Read

- [Design Doc — Feasibility Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report) (Part 4: Validation Design)
- [Data Assessment — Testdaten](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)

---

## Discussion Topics

*Einstiegspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Aus den gelieferten 14 Projekten wählen wir Testanfragen

Wir haben 14 Projektordner erhalten (Anfragen + Angebote + Dateien). Für die Validierung brauchen wir eine Referenztabelle — die Evaluierungsliste. Die Idee: Wir nehmen 10 Projekte als bekannten Datenbestand. Dann simulieren wir eine neue Anfrage (z.B. die 11. Anfrage) und prüfen: Findet das System die richtigen ähnlichen Projekte aus den 10?

Damit wir auswerten können ob das Ergebnis stimmt, brauchen wir von Ihnen vorab die erwarteten Treffer. Konkret: "Für diese Testanfrage erwarten wir, dass Projekt X als ähnlichstes zurückkommt — weil Gebäudetyp und Höhe übereinstimmen." Dafür haben wir eine CSV-Vorlage vorbereitet mit 4 Spalten: Testanfrage, Erwarteter Treffer, Fehlfährte (ja/nein), Begründung.

### 2. Das System muss auch "nichts gefunden" sagen können

Ein gutes Ergebnis ist nicht immer ein Treffer. Wenn eine völlig neue Anfrage kommt — ein Gebäudetyp den wir noch nie gebaut haben, oder ein Kunde ohne Referenzhistorie — dann ist die richtige Antwort: "Dafür haben wir kein vergleichbares Projekt." Wir brauchen Testfälle, bei denen genau das erwartet wird. Ob diese aus den bestehenden 14 Projekten kommen oder als neue Szenarien konstruiert werden, ist offen.

## Meeting Format

- **Typ:** Arbeitssitzung
- **Teilnehmer:** Herr Sasse + Marius (ggf. Kalkulator)
- **Erwartung:** Herr Sasse kommt mit Kenntnis der 14 gelieferten Projekte und einer Einschätzung, welche davon "ähnlich" zueinander sind
- **Ergebnis:** Gemeinsames Verständnis der Evaluierungsmethode und Einigung auf den Inhalt der Evaluierungsliste. Die CSV wird anschließend von REKERS ausgefüllt.

## Related

- **Issue:** [#629 — REKERS: KI-gestütztes Angebotssystem - Workshop 2](https://github.com/DaveX2001/deliverable-tracking/issues/629)
- **Design Doc:** [Feasibility Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Data Assessment:** [Testdaten Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)
