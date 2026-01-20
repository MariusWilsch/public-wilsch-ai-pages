---
publish: true
---

# Pflichtenheft: DS-Kit Navigation – Erweiterung
[[client-iitr]]

*Technische Spezifikation für Erweiterung des bestehenden DS-Kit Navigationssystems*

---

## 1. Deckblatt

| | |
|---|---|
| **Projekt** | DS-Kit Navigation – Erweiterung |
| **Auftraggeber** | IITR Datenschutz GmbH |
| **Auftragnehmer** | Wilsch AI Services OÜ |

---

## 2. Zielbestimmung

### Muss (Erweiterungsumfang)
- Website-Extraktion: Kapitel von core.iitr.de extrahieren und indexieren
- Support-Eskalation: "Support kontaktieren"-Button bei unsicheren Antworten

### Später (Wartet auf Kundenzulieferung)
- Integration neuer Masterfragen – wartet auf Zulieferung von Stellmacher (~Ende Januar)
- Zusätzliche Q&A-Paare – Stellmacher liefert erweiterte CSV mit Detailfragen

### Außerhalb Umfang
- Telefon-Support oder Live-Chat
- Automatische Synchronisation mit core.iitr.de
- Änderungen an bestehender POC-Funktionalität

---

## 3. Funktionale Anforderungen

### Website-Extraktion (core.iitr.de)

- **FR-E01:** Alle DS-Kit-relevanten Kapitel aus core.iitr.de extrahieren
- **FR-E02:** Extrahierte Inhalte in Typesense indexieren, Genauigkeit bei DATA-GAP-Fragen verbessern (Q6, Q12, Q13, Q17, Q18, Q24, Q27)

### Support-Eskalation Level 2

- **FR-E03:** Langfuse-Observability-Filter für Gesprächsverlauf deployen
- **FR-E04:** "Support kontaktieren"-Button mit E-Mail-Link und Session-Kontext anzeigen
- **FR-E05:** Auto-Vorschlag des Support-Buttons bei unsicheren Antworten

---

## 4. Aufwand und Preis

| | |
|---|---|
| **Aufwand geschätzt** | ~4 Stunden |
| **Preis** | EUR 800 (Fixpreis) |

---

*WILSCH AI SERVICES OÜ | Harju maakond, Tallinn, Sepapaja tn 6, 15551*
