---
publish: true
---

# Meeting-Agenda: Ulrich Sync — 30. März 2026
[[client-wilsch-group]]

## Meeting-Ziel

Acht Punkte besprechen: Neuer Vertrag ab April, offene Rechnungen, Seminar-Ergebnisse nutzen, und fünf operative Themen abarbeiten.

1. **Vertrag** — Neuen Rahmenvertrag für April besprechen
2. **Rechnungen** — Rekers + DGX Spark abrechnen
3. **FSO-Seminar** — Vier interessierte Kontakte, nächste Schritte
4. **Kundendaten** — Datensicherheit klären
5. **NDA David** — Anwalt reagiert nicht, alternatives Vorgehen
6. **IBM Power / Kernel-Entwickler** — Ralph einbinden
7. **KI aus der Box + Rekers** — Demo-Status und Preisschätzung
8. **Newsletter** — Richtung festlegen

## Vorab-Lektüre

- [FSO-Seminar Email (29. März)](https://mail.google.com/mail/u/0/#sent/FMfcgzQgKvPzbMTpTfQMDZTrbqkTmXNz) — Seminar-Ergebnisse und Rückmeldungen
- [Vertragsdiskussion (Februar)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-contract-consolidation) — Vorherige Besprechung zur Vertragskonsolidierung

---

## Besprechungspunkte

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Rahmenvertrag ab April
⏱️ 10 min

Der aktuelle Supportvertrag (Dezember 2025) läuft Ende März aus. Ziel: alle laufenden Arbeitsströme in einen einzigen Vertrag zusammenführen.

- **Vorschlag:** 5 Stunden/Woche für Ulrich + 5 Stunden/Woche für Thomas (mit Option auf 7,5h)
- **Umfasst:** Rechnungsverarbeitung, TANSS-Anbindung, Infrastruktur-Wartung, täglicher Austausch
- **Davids Vertrag:** Erst möglich, wenn das Insolvenzverfahren seines früheren Arbeitgebers abgeschlossen ist

**Zu klären:** Was genau soll der neue Vertrag abdecken? Was bleibt draußen? Startdatum und Konditionen.

### 2. Offene Rechnungen
⏱️ 5 min

Zwei Positionen stehen zur Abrechnung:
- **Rekers** — Rechnung für die abgeschlossene Machbarkeitsstudie
- **DGX Spark** — Rechnung für das KI-Hardware-System

**Zu klären:** Beträge, Empfänger und wann die Rechnungen gestellt werden.

### 3. FSO-Seminar — Ergebnisse und Follow-up
⏱️ 5 min

Das Seminar am 27. März hat vier konkrete Rückmeldungen gebracht:

| Wer | Firma | Interesse | Vorgeschlagener nächster Schritt |
|-----|-------|-----------|----------------------------------|
| Eckehard Kraska | IITR | On-Premise-Avatare für seine Dginiis-Linie — „Rechnen Sie bitte damit, dass wir Sie beauftragen" | Termin in München vereinbaren |
| Steffen Arndt | AMARI Metall | „Das Thema ist für mich sehr interessant. Gern möchte ich das Gespräch vertiefen." | Gespräch ansetzen |
| Sebastian Kraska | IITR | On-Premise-Avatare als besonders interessant hervorgehoben | Gemeinsam mit Eckehard besprechen |
| Harald Deckers | — | Hat das Seminar verpasst (Einladungslink im Spam), möchte Zugang zur Webseite | Zugang einrichten, nächstes Seminar einladen |

**Was wir gezeigt haben:** Interaktive KI-Demo auf dem DGX Spark, Hardware-Benchmarks mit echten Kundendaten (294 Seiten), Vergleichsseiten auf der Webseite.

**Zu klären:** Wer kontaktiert wen? Soll Ulrich beim Kraska-Termin in München dabei sein? Wann planen wir das nächste Seminar?

### 4. Kundendaten auf GitHub
⏱️ 5 min

Ulrich hat am 11. März seine Sorge geäußert, dass Kundendaten von Rekers und AVO auf GitHub liegen könnten — Stichwort Datenschutz und Haftung.

**Zu klären:** Welche Daten liegen wo? Was muss migriert werden? Welches Sicherheitsniveau ist akzeptabel?

### 5. NDA für David
⏱️ 5 min

Die Vertraulichkeitsvereinbarung für David ist als Entwurf fertig. Herr Lemke (Anwalt) wurde am 1. März um Prüfung gebeten, hat aber seit über vier Wochen nicht geantwortet. Marius hat am 28. März nochmal nachgehakt.

**Zu klären:** Warten wir weiter auf Lemke? Oder prüft Ulrich den Entwurf direkt, damit wir vorankommen?

### 6. IBM Power — Kernel-Entwickler einbinden
⏱️ 5 min

Nach dem FSO-Seminar ist das Thema KI-Inferenz auf IBM Power 10 wieder aktuell. Nächster Schritt: Ralph muss mit ins Boot — er braucht Benchmarkdaten (wie lange eine Dokumentenverarbeitung aktuell dauert), um das Optimierungspotenzial einschätzen zu können.

- **Ziel:** Verarbeitungsgeschwindigkeit von ~55 auf ~500 Tokens/Sekunde steigern
- **Team:** Marius, Kernel-Entwickler, Thomas Erhard
- **Offen:** NVLink-Kabel wird für Multi-GPU-Betrieb benötigt

**Zu klären:** Wie bringen wir Ralph an den Tisch? Wer liefert ihm die Benchmarkdaten? Budget für Kabel und Entwickler-Zusammenarbeit?

### 7. KI aus der Box + Rekers-Preisschätzung
⏱️ 5 min

**Demo-System:**
Das „KI aus der Box"-System auf dem DGX Spark läuft — KI-Modell ist deployed und getestet (64,7 Tokens/Sekunde). Nächster Schritt: Chat-Oberfläche und Sprachsteuerung fertigstellen. Das Paket für Kunden: Hardware (5.000 €) + Einrichtung und Schulung in 5 Tagen (5.000 €). Harald Deckers hat bereits um Zugang gebeten.

**Rekers-Preisschätzung:**
Das geplante Review am 23. März mit Herrn Jäger, Sasse und Withake hat nicht stattgefunden. Telefonat mit Jäger am 25. März war positiv: „Geht in die richtige Richtung!" Neuer Termin voraussichtlich Ende April (KW16/17) — Ostern und Urlaub verhindern ein früheres Treffen. Preisschätzung muss bis dahin stehen.

**Zu klären:** Soll die Chat-Oberfläche weiter ausgebaut werden? Zugang für Deckers einrichten? Preisrahmen für Rekers vor dem April-Termin festlegen?

### 8. Newsletter
⏱️ 5 min

Im Februar hatten wir besprochen, einen regelmäßigen Newsletter für Mittelstandskunden aufzubauen — Thema: praktische KI-Anwendungen mit lokaler Hardware (z.B. unsere Inferenz-Benchmarks als Inhalt). Seitdem ist das Thema liegengeblieben.

Vier offene Punkte:
- Inhaltsstrategie und Format festlegen
- Deutschsprachige Person für Kundenansprache und Vertrieb finden
- DSGVO-konforme Verteiler-Aufbau
- Regelmäßiger Produktionsprozess

Parallel wurde eine duale Vertriebsstrategie vorgeschlagen: Self-Service-Tools für kleinere Firmen + individuelle Betreuung für größere Kunden.

**Zu klären:** Ist der Newsletter noch Priorität? Falls ja: welcher inhaltliche Schwerpunkt? Soll Ulrichs Kundennetzwerk für den Verteiler genutzt werden?

---

## Meeting-Format

- **Typ:** Blocker-Sync / Entscheidungs-Meeting
- **Dauer:** 45 Minuten (14:15–15:00)
- **Teilnehmer:** Ulrich Wilsch, Thomas Erhard, Marius Wilsch, David Eberle
- **Erwartung:** Klare Entscheidungen zu Vertrag, Rechnungen, Datensicherheit und NDA
- **Ergebnis:** Action Items pro Thema mit Verantwortlichen

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

