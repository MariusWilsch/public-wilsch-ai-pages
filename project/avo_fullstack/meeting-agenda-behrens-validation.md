---
publish: true
---

# Meeting-Agenda: Validierungspaare für Soßen Sourcing POC
[[client-avo]]

## Meetingziel

Validierungsdaten von Herrn Behrens erhalten, um den Rezeptähnlichkeits-Algorithmus zu kalibrieren und zu testen.

1. **Bewertungspaare erhalten** — 5 bekannte ähnliche Rezeptpaare mit Konfidenzangabe und Begründung bei Ankerpaaren
2. **Negativbeispiele identifiziert** — 1–2 Paare, die Zutaten teilen, aber NICHT ähnlich sind (falls vorhanden)

## Diskussionsthemen

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Das zeigt unser Algorithmus Ihnen zurück

Wir haben ein Rezeptvergleichssystem entwickelt, das für jede Rezeptur die 5 ähnlichsten findet und zeigt, warum sie ähnlich sind. Hier ein Beispiel der Ausgabe:

```
Abfrage: R261800

Rang  Rezeptur  Überlappung  Anteil   Kategorie  Gesamt
#1    R261700   85%          97%      N/A        92%
#2    R482000   62%          78%      N/A        71%
#3    R954700   58%          82%      N/A        68%
#4    R445100   51%          65%      N/A        57%
#5    R887200   48%          71%      N/A        55%
```

- **Überlappung:** Wie viele Zutaten beide Rezepturen gemeinsam haben
- **Anteil:** Wie nah die Mengen bei gemeinsamen Zutaten beieinander liegen
- **Kategorie:** Ob nicht-gemeinsame Zutaten zur selben Einkaufsgruppe gehören

### 2. Zur Kalibrierung benötigen wir bekannte Rezeptpaare von Ihnen

Um zu testen, ob der Algorithmus die richtigen Ergebnisse liefert, benötigen wir bekannte ähnliche Paare aus Ihrer Erfahrung. Wir haben eine einfache Tabelle vorbereitet:

| Rezeptur_A | Ähnlichste (Pflicht) | Weitere (optional) | Konfidenz | Warum? (bei „Definitiv") |
|------------|---------------------|-------------------|-----------|--------------------------|
| R...       | R...                | R..., R...        | Definitiv / Wahrscheinlich / Unsicher | Kurze Notiz |

Für jedes Paar benötigen wir die Rezeptur-IDs und Ihre Einschätzung, wie sicher Sie sich sind. Bei Paaren, bei denen Sie sich am sichersten sind, hilft uns eine kurze Begründung zu verstehen, was „ähnlich" aus fachlicher Sicht bedeutet.

### 3. Eine Mischung verschiedener Paartypen hilft bei der Kalibrierung

Wir erhalten den größten Kalibrierungswert aus einer Mischung von Paaren — nicht nur aus den offensichtlichsten Duplikaten. Wenn manche Paare sehr ähnlich und andere eher grenzwertig sind, hilft uns diese Bandbreite, den Algorithmus über das gesamte Spektrum abzustimmen.

### 4. Paare, die auf dem Papier ähnlich aussehen, es aber nicht sind

Falls Sie Rezeptpaare kennen, die viele Zutaten teilen, die Sie aber nicht als ähnlich betrachten würden — zum Beispiel Rezepturen, die dieselbe Grundsoße für völlig unterschiedliche Produktlinien verwenden — würden uns auch 1–2 solcher Beispiele helfen zu testen, ob der Algorithmus falsche Treffer korrekt ablehnt.

## Meetingformat

- **Art:** Discovery — Erhebung von Validierungsdaten
- **Erwartung:** Herr Behrens kommt mit Wissen darüber, welche Rezepturen er als ähnlich betrachtet
- **Ergebnis:** Ausgefüllte Bewertungstabelle + optionale Negativbeispiele

## Verknüpfungen

- **Issue:** [#513: AVO: KI_PROJEKT_SOSSEN_SOURCING POC](https://github.com/DaveX2001/deliverable-tracking/issues/513)
- **Design Doc:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/design-doc-sossen-sourcing-poc)
