---
publish: true
---

# Meeting Agenda: Datenbestätigung für Soßen Sourcing POC
[[client-avo]]

## Ziel des Meetings

Am Ende dieses Meetings sollten wir haben:

1. **Datenumfang bestätigt** — welche Einträge aus alle.jsonl sind Fertigprodukte für den Vergleich
2. **Materialbehandlung vereinbart** — wie Zutatenvarianten (z.B. 00004 vs 00004B) im Ähnlichkeitsvergleich behandelt werden
3. **Datenaufbereitung geklärt** — ob Basisvariante-Filterung auf die Testdaten angewendet wurde

---

## Diskussionsthemen

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Der Vergleichspool enthält 62 R-Präfix-Einträge

Aus alle.jsonl (197 Einträge) haben 62 ein R-Präfix (Fertigprodukte), 18 RV (Vormischungsrezepte), 9 RZ (Zwischenmischungen), 6 B-Präfix (nicht klassifiziert).

Wir möchten bestätigen:
- Sind diese 62 die vollständige Menge der Fertigprodukte in den Testdaten?
- Was sind B-Präfix-Einträge — sollten welche davon im Vergleichspool sein?

### 2. Basisvariante-Filterung in den Testdaten

Das Design sieht vor, dass nur Variante 0 (Grundrezeptur) in den Vergleich eingeht. Unsere Analyse hat keine Variantenduplikate unter den 62 R-Einträgen gefunden.

Wir möchten bestätigen:
- Wurde Variante-0-Filterung bei der Erstellung von alle.jsonl angewendet?

### 3. Materialien mit Suffixvarianten

00004 und 00004B erscheinen als separate Material-IDs. Der POC behandelt sie als unterschiedliche Zutaten — sie matchen nicht miteinander im Ähnlichkeitsvergleich.

Wir möchten verstehen:
- Ist diese Behandlung korrekt?
- Sind Suffixvarianten immer derselbe Grundstoff in unterschiedlichen Qualitätsstufen?

### 4. Stammdaten-CSV für zukünftigen Kategorievergleich

Kategorie-Gruppierung (Einkaufsgruppen) wird für den POC-Start nicht benötigt, würde aber ermöglichen zu testen, ob das Erkennen austauschbarer Zutaten die Ergebnisse verbessert.

- Wann kann die Stammdaten-CSV geliefert werden?

---

## Meeting-Format

- **Art:** Bestätigungssession
- **Erwartung:** Mattis prüft Datenaufbereitungsentscheidungen
- **Ergebnis:** Freigabe der Datenannahmen für die POC-Implementierung

---

## Verknüpft

- **Issue:** [#513 - AVO: KI_PROJEKT_SOSSEN_SOURCING POC](https://github.com/DaveX2001/deliverable-tracking/issues/513)
- **Design Doc:** [Soßen Sourcing POC](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/design-doc-sossen-sourcing-poc)
