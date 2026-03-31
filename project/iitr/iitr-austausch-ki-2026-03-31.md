---
publish: true
---

# Austausch KI — IITR Bi-Weekly Sync (31. März 2026)
[[iitr-austausch-ki-2026-03-31]]

Bi-weekly Sync mit Marvin Stellmacher, Sebastian Kraska und Eckehard Kraska. Zweiteiliges Programm: DS-Kit-Liefermeilenstein + Avatar-Strategie.

---

## Meeting Goal

Zwei Ergebnisse definieren einen erfolgreichen Termin heute:

1. **Liefermeilenstein vereinbart** — Stellmacher bestätigt morgen 12:00 als formalen Abnahmezeitpunkt für Navigation + Masterfragen auf Staging
2. **Avatar-Bedarf validiert** — Budget, Zeitplan und Umfang bestätigt; Münchner Vertiefungsgespräch für KW 15/16 geplant

## Pre-Read

- [DS-Kit Navigation Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-design) — Navigation + CJ Architektur
- [MF Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/masterfragen-design) — Masterfragen Serving Layer
- [#1306 MF Serving](https://github.com/DaveX2001/deliverable-tracking/issues/1306) — AC1–5 bestätigt, Merge heute
- [#1303 Avatar Spike](https://github.com/DaveX2001/deliverable-tracking/issues/1303) — DGX Spark Machbarkeitsprüfung
- E-Mail Eckehard Kraska (29. März) — vollständige Dginii-Vision + Beauftragungsabsicht

---

## Diskussionspunkte

*Einstiegspunkte für die Diskussion, nicht abschließend.*

### 1. DS-Kit Lieferstand — Formaler Abnahmezeitpunkt
⏱️ 20 min

Navigation, Urteile und Masterfragen sind alle live oder landen heute auf Staging. Die Infrastruktur ist stabil. Die ausstehende Arbeit — finale Prompt-Fixes für Navigation — wird bis morgen 12:00 deployed. Das ist der vorgeschlagene formale Abnahme-Gate: Stellmacher testet die deployed Version und bestätigt.

- Navigation + CJ: live auf Staging — Stellmacher getestet am 30. März ("gute Ergebnisse")
- Masterfragen: AC1–5 auf Preview bestätigt, Merge auf Staging heute
- Langfuse-Monitoring: Deployment nach dem Meeting — zentrale Nachverfolgbarkeit folgt
- Morgen 12:00: finale Prompt-Fixes deployed → Stellmacher validiert → Übergabe

**To resolve:** Bestätigung von morgen 12:00 als formalem Abnahmezeitpunkt für Navigation + Masterfragen.

### 2. Konsolidierte Infrastruktur als Fundament
⏱️ 5 min

Alle drei DS-Kit-Projekte — Navigation, Urteile, Masterfragen — laufen jetzt auf einem gemeinsamen GPU-Staging-System. Diese Konsolidierung war keine operative Vereinfachung, sondern eine architektonische Voraussetzung. Ein On-Premise-Avatar kann nicht als drei separate Chat-Schnittstellen funktionieren — er braucht ein einheitliches Backend, das Anfragen über alle drei Wissensdatenbanken hinweg routen kann.

- Gemeinsame GPU-Hardware (RTX 4000 SFF Ada): ein Hardware-Pool für alle Projekte
- Zentrales Langfuse-Dashboard: Nachverfolgbarkeit über alle drei Pipelines
- Routing-Schicht: das Fundament, das projektspezifische Dginii-Antworten ermöglicht

**To resolve:** Gemeinsames Verständnis, dass die Infrastruktur-Konsolidierung die architektonische Voraussetzung für die Dginii-Avatar-Integration ist.

### 3. Avatare / On-Premise — Dginii-Strategie
⏱️ 30 min

Eckehard Kraska hat am 29. März die vollständige Dginii-Architektur beschrieben: Personas in Erklärfilmen und interaktiven Schulungsmodulen, derzeit über Akool (USA) animiert und mit OpenAI verbunden. Der Kern der Anfrage: Akool liefert keine On-Premise-Lösung, obwohl sie es zugesagt haben — und ohne On-Premise können datenschutzrelevante Fragen nicht EU-konform verarbeitet werden. Das würde die gesamte Dginii-Linie blockieren.

- Akool-Alternative: HunyuanVideo-Avatar (Tencent) und Duix.Heygem — beide On-Premise, API-kompatibel, mehrsprachig inkl. Deutsch
- DGX Spark Hardware verfügbar für Machbarkeitsprüfung
- Größere Vision: Dginii als einheitliche Auskunftsmaschine für 2.500 Kunden — kombiniert eigene DS-Kit-Entwicklung + LLM + Avatar-Animation
- Budgetrahmen und Zeitplan noch nicht bestätigt

**To resolve:** Validierung des konkreten Bedarfs — Budget, Zeitplan und Umfang (enger Akool-Ersatz vs. vollständige Dginii-Produktentwicklung) sowie Abstimmung des Münchner Termins für die technische Vertiefung.

## Meeting Format

- **Art:** Bi-weekly Sync — Review + Strategiegespräch
- **Dauer:** 60 Minuten
- **Teilnehmer:** Marvin Stellmacher, Sebastian Kraska, Eckehard Kraska + Marius Wilsch
- **Ablauf:** DS-Kit Lieferstand (25 min) → Infrastruktur-Brücke (5 min) → Dginii-Avatar-Strategie (30 min)
- **Ergebnis:** Abnahme-Termin für morgen 12:00 vereinbart + nächste Schritte für Avatar-Beauftragung definiert

## Related

- **Epic:** [#959 — IITR Contract Extension & Stabilization](https://github.com/DaveX2001/deliverable-tracking/issues/959)
- **Issue:** [#1093 — JA Design Doc Extraction Passes](https://github.com/DaveX2001/deliverable-tracking/issues/1093)
- **Issue:** [#1303 — On-Premise Avatar Spike](https://github.com/DaveX2001/deliverable-tracking/issues/1303)
- **Design Doc:** [DS-Kit Navigation Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-design)
- **Design Doc:** [Masterfragen Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/masterfragen-design)
- **Session:** `623779f8-99ba-4260-b6ae-934c857426a1`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

