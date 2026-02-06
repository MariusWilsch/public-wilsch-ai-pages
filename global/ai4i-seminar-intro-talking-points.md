---
publish: true
---
# AI4i Seminar – Vorstellung Marius Wilsch
[[client-wilsch-group]]

## Sprechernotizen (~2 Min.)

### 1. 42 University, Marokko (~20s)
- Studium an der 42 – keine Lehrer, keine Vorlesungen
- Prinzip: Du bekommst ein Problem, du findest die Lösung
- Selbstständiges Denken als Grundlage

### 2. ChatGPT, November 2022 (~20s)
- 3 Monate ins Studium, dann kommt ChatGPT
- Sofort klar: Das verändert alles
- Nicht nur ein Tool – ein neues Paradigma

### 3. Der Klick-Moment (~30s)
- 42 hat den Bedarf geschaffen: Probleme selbst lösen, schnell lernen
- KI hat genau diesen Bedarf erfüllt – sofort anwendbar
- Keine Theorie, sondern direkter Einsatz vom ersten Tag an
- Seitdem: KI ist mein tägliches Werkzeug

### 4. Evolution durch Kundenprojekte (~20s)
- Jedes Projekt vertieft die Expertise
- Das Feld ist erst 3 Jahre alt – wir lernen alle noch
- Praxiserfahrung schlägt jede Zertifizierung

### 5. Team-Brücke (~20s)
- Ich bringe die KI-Tiefe mit
- Thomas Erhard bringt die Systemkompetenz – IBM Power, Infrastruktur
- Ulrich Wilsch bringt 66 Jahre Geschäftserfahrung und Kundenbeziehungen

### 6. Teaser + Abschluss (~10s)
- Gleich zeigen wir Ihnen live, was damit möglich ist
- Aber zuerst: die Grundlagen

---

## Live Demo: Chat2DB auf IBM POWER (~10 Min.)

### Choreografie
- **Ulrich** rahmt das Geschäftsszenario ("Stellen Sie sich vor, ein Kunde ruft an...")
- **Marius** tippt die Frage in OpenWebUI
- **Marius** erklärt die Antwort mit **Vorher/Nachher-Kontrast**

### Erklärungswinkel
Jede Antwort: "Das hat vorher X gedauert — jetzt ist es eine Frage auf Deutsch."
- Nicht die Technik erklären (Vogelperspektive)
- Den Zeitvorteil betonen
- SQL ist ausgeblendet, auf Wunsch zeigbar

### Wow-Faktor
Deutsche natürliche Sprache → korrekte Ergebnisse aus strukturierten Daten.
Das Publikum kennt die kryptischen Spaltennamen (FABR40, KLAS40, AUSR40) — NL-Abfragen sind der Kontrast.

### 3 Fragen (validiert 2026-02-06)

**Frage 1 — Nachschlagen (Utility)**
> An welchen Maschinen (MA) kann der Adapter EC2U angeschlossen werden?

- Ergebnis: 9009 und 9105
- Vorher/Nachher: "Normalerweise durchsucht man Tabellen manuell — jetzt eine Frage"

**Frage 2 — Auflisten (Exploration)**
> Zeige mir alle Maschinen (MA) in der Prozessorklasse P05?

- Ergebnis: 9028 (Power10 S1012, 4 Kerne)
- Vorher/Nachher: "Statt SQL-Kenntnisse braucht man nur die richtige Frage"

**Frage 3 — Vergleich & Aggregation (Intelligenz)**
> Welche Maschine hat in der Prozessorklasse P10 die meisten Kerne?

- Ergebnis: 9824 (Power11 S1122, 2x30 = 60 Kerne)
- Vorher/Nachher: "Das Modell vergleicht, rechnet, und gibt eine klare Antwort"
- Stärkstes Demo-Moment: zeigt Reasoning, nicht nur Lookup

### Narrative Steigerung
`Nachschlagen → Auflisten → Vergleichen + Rechnen`
Jede Frage zeigt eine anspruchsvollere Fähigkeit.

### Nach den 3 Fragen
- Live-Fragen aus dem Publikum zulassen
- Zeigt Vertrauen in das System
- Ulrich moderiert: "Hat jemand eine Frage an die Datenbank?"

### Technische Vorbereitung
- `make tunnels` vor der Präsentation (SSH-Tunnel zu IBM POWER)
- `make remote-test` zur Validierung der Chain
- mcpo ist instabil — bei Fehler: `ssh IBM-POWER 'make mcpo-stop && sleep 2 && make mcpo-start'`
- OpenWebUI: http://localhost:3000
- Bandbreite: VPN minimieren, andere Downloads stoppen
