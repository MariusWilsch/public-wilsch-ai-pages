---
publish: true
---

# Meeting-Agenda: Ulrich Sync — 2. April 2026

[[client-wilsch-group]]

## Meeting-Ziel

> **Meeting abgeschlossen am 2. April 2026, 07:00–08:25.** Ergebnisse im [Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-ulrich-sync-2026-03-30) dokumentiert.

Follow-up zum 30. März: Fünf offene Punkte abarbeiten — Vertrag, NDA, Kate, Kundendaten, Newsletter. Zwei neue Themen dazu: Ostermann (Doku + Testumgebung) und Angebotsautomatisierung. Dazu Status-Runde zu den erledigten Action Items.

1. **Vertrag** ⏳ — FDI-Modell besprochen, 2 Wochen Designphase
2. **NDA David** ✅ — Thomas weist Chris an, auf NDA zu antworten
3. **Kate/Malaysia** ⏳ — Ulrich spricht Kate über Ostern
4. **Kundendaten** ✅ — DMZ-Architektur entschieden, Meeting Di nach Ostern
5. **Newsletter** ⏳ — Marius Marketing-Projekt, 3–6 Monate
6. **Ostermann** 🪦/⏳ — Testumgebung begraben, Doku Ulrich über Ostern
7. **Angebotsautomatisierung** ⏳ — Q4/Jahresende verschoben
8. **Status-Runde** ✅ — Rechnungen geklärt, AWO 10. April Meeting

## Vorab-Lektüre

- [Design Doc: Ulrich Sync 30. März](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-ulrich-sync-2026-03-30) — Entscheidungen und offene Punkte
- [DGX Spark Infrastructure Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-dgx-spark-infrastructure) — Neu seit 31. März

---

## Besprechungspunkte

_Offene Punkte vom 30. März + neue Entwicklungen._

### 1. Rahmenvertrag ab April

⏱️ 15 min

Der aktuelle Vertrag wurde am 30. März um einen Monat verlängert (€1.250). Das Design-Partner-Modell (Retainer + Success Fee, Palantir FDI) war in zwei Wochen fällig — Zwischenstand offen.

- Ulrich: 10% Sales-Provision, keine Projektverantwortung
- Thomas: Fakturadresse (Rechnungsempfänger)
- Marius + Thomas: 90% Projektaufteilung (genaue Aufteilung noch offen)
- Produktlinien-Diskussion war für Samstag/Ostern geplant
- Vertrag läuft seit 31. März auf Verlängerungsbasis

**Zu klären:** Vertragsbedingungen, Design-Partner-Modell-Zwischenstand, und Marius/Thomas-Aufteilung pro Projekt festlegen.

### 2. NDA für David

⏱️ 10 min

Am 30. März komplett übersprungen — Meeting-Zeit reichte nicht. Herr Lemke (Anwalt) hat seit über 5 Wochen nicht geantwortet (letzte Nachfrage: 28. März).

- NDA-Entwurf liegt fertig vor
- Lemke seit 1. März ohne Reaktion trotz Nachfrage
- Separat: Lemke hat auch bei Rekers/Witak-NDA keine Rückmeldung bekommen (Thomas, 30.03.)

**Zu klären:** Vorgehen ohne Lemke — Ulrich prüft Entwurf direkt oder Frist setzen.

### 3. Kate / Malaysia-Expansion

⏱️ 5 min

Am 30. März: €190/h wurde als zu hoch bewertet für eine Contact-Brokering-Rolle. Ulrich wollte Kate bis Fr/Sa (28./29. März) direkt kontaktieren.

- Kates Vertrag läuft Ende April aus — Zeitdruck
- IBM-SSD-Preise um 15–55% gestiegen, chinesische Einkäufer gestoppt
- Malaysia zeigt aktive Nachfrage

**Zu klären:** Ergebnis von Ulrichs Gespräch mit Kate — Go/No-Go für die Engagement-Verlängerung.

### 4. Kundendaten auf GitHub

⏱️ 5 min

Am 30. März nicht als eigenständiges Thema besprochen. Nur indirekt: GitHub explizit ausgeschlossen für Kundendaten, self-hosted GitLab als Alternative genannt.

- Ulrichs Sorge vom 11. März: Rekers/AVO-Daten auf GitHub
- Aktuell keine Bestandsaufnahme, welche Daten wo liegen
- DGX Spark zieht im April ins Rechenzentrum — Timing für Datenmigration

**Zu klären:** Bestandsaufnahme Datenstandorte und Entscheidung zur Repository-Lösung (GitLab, eigener Server).

### 5. Newsletter-Strategie

⏱️ 5 min

Am 30. März als „begraben" markiert (3–6 Monate). Korrektur: Nicht begraben — in zwei Wochen revisiten (Mitte April), passt zum April-Marketing-Fokus.

- 10 Test-Zugänge (FSO-Seminar) als leichtgewichtiger Pilot für Content-Distribution
- Offene Frage: Monetarisierung — was wäre der monatliche Verkaufspreis?
- Kein Owner, kein Kapazitätsplan

**Zu klären:** Monetarisierungsrahmen und Verantwortlichkeit für den Newsletter-Piloten festlegen.

### 6. Ostermann: Doku + Testumgebung

⏱️ 5 min

Zwei offene Themen zu Ostermann, kombiniert:

**Dokumentation (#866):** Ulrich sollte die Roh-Dokumentation senden (Passwörter raus), damit David die Doku per AI aufwerten kann — gleicher Ansatz wie beim Design Doc. Seit Feb. 19 wartend.

**Testumgebung (#1059):** Ostermann-Maschine als zweite Testumgebung für UWI-Retainer-Arbeit einrichten. Ermöglicht paralleles Testen ohne Konflikte auf der Primärumgebung.

**Zu klären:**
- Hat Ulrich die Roh-Dokumentation vorbereitet?
- Zugang zur Ostermann-Maschine: Credentials, Scope, was soll darauf getestet werden?

### 7. Angebotsautomatisierung — Portal Wilsch

⏱️ 5 min

Thomas Erhard schreibt jedes Hosting-Angebot von Hand — viele Abschnitte wiederholen sich kundenübergreifend (#640). Drei bestehende Angebote liegen als PDFs vor (Hellmann, Holz Henkel, OSM). Möglicher AI/Template-Ansatz könnte den Prozess beschleunigen.

**Zu klären:** Ist Angebotsautomatisierung aktuell Priorität für Ulrich, oder parken wir das?

### 8. Status-Runde

⏱️ 10 min

Kurzer Check der Action Items vom 30. März:

| Thema | Action | Owner | Status |
|-------|--------|-------|--------|
| **Rechnungen** | Rekers 40% Split bestätigen | Thomas | ✅ Bestätigt (40%) |
| **Rechnungen** | Rekers-Rechnung senden | Thomas | ✅ 2. April |
| **Rechnungen** | Witak anrufen (Entscheider-Team) | Ulrich | ⏳ Offen |
| **Rechnungen** | DGX in Rechenzentrum umziehen | Thomas | ⏳ April geplant |
| **FSO-Seminar** | Kontakte nachtelefonieren | Ulrich | ✅ Leads aktiv (Hasloff, Poggemüller, Amari) |
| **FSO-Seminar** | 10 Website-Zugänge vergeben | Ulrich | → Evolviert zu #1334 (30-Tage Prospect Accounts) |
| **KI aus der Box** | PDF-Angebotsgenerator | David | ⏳ Wartet auf Ulrichs Referenz-Dokumente |
| **KI aus der Box** | 4 Artikel in Warenwirtschaft | Ulrich/Busch | ⏳ Offen |
| **AWO** | DGX Kabel testen + Modell installieren | Marius | ✅ Hardware läuft, Model-Sharding offen (Ziel: 10. April) |
| **Placetel** | Frank Lasinski Setup | Ulrich | ❌ Nicht besprochen |

**Neue Entwicklung:** IITR Kraska-Meeting am 31. März erfolgreich — On-Premise Avatar-Lösung besprochen, nächstes Meeting 14. April. (Direkter FSO-Seminar Follow-up Erfolg!)

**Korrektur:** DGX Spark Miet-Modell (€500/Monat) ist nicht einmalig — läuft monatlich pro Projekt weiter (z.B. auch für Rekers Step 2 Angebot).

---

## Meeting-Format

- **Typ:** Follow-up Sync / Offene Punkte
- **Dauer:** 65 Minuten (07:00–08:05)
- **Teilnehmer:** Ulrich Wilsch, Thomas Erhard, Marius Wilsch, David Eberle
- **Erwartung:** Entscheidungen zu Vertrag und NDA, Status-Update Kate, Klärung Kundendaten und Newsletter, Ostermann-Status, Angebotsautomatisierung-Prio
- **Ergebnis:** Action Items pro Thema mit Verantwortlichen

---

## Referenz

- [Design Doc: Ulrich Sync 30. März](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/design-doc-ulrich-sync-2026-03-30)
- [Meeting-Agenda 30. März](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-ulrich-sync-2026-03-30)
- [#1312](https://github.com/DaveX2001/deliverable-tracking/issues/1312)

🤖

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

