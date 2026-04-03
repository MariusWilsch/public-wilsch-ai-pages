---
publish: true
---

# Kundengespräch: IBM 4247 Druckeranalyse
[[client-02_uwi]]

## Meeting Goal

Ulrich klärt mit seinem Kunden die offenen Punkte, die für eine konkrete Farbband-Bestellung und Migrationsentscheidung nötig sind.

1. **Druckeridentifikation** — exakte Modellnummern aller IBM 4247 im Einsatz
2. **Bedarfsanalyse** — welche Formulare gedruckt werden und ob Durchschläge nötig sind
3. **Migrationspfad-Auswahl** — auf Basis der Ergebnisse den passenden Pfad (A/B/C) wählen

## Pre-Read

- [Design Doc: IBM 4247 Druckerband-Beschaffung + Matrixdrucker-Ablösung](design-doc-printer-research-1375)

---

## Discussion Topics

*Ausgangspunkte für das Gespräch, nicht darauf beschränkt.*

### 1. Die Drucker im Einsatz
⏱️ 5 min

Das Design Doc listet drei verschiedene Farbbandtypen für den IBM 4247 — welcher bestellt werden muss, hängt vom exakten Modell ab.

- IBM 4247-001/003 nutzen Farbband 1053685 (15 Mio. Zeichen)
- IBM 4247-V03/X03/Z03 nutzen Farbband 57P1743 (20 Mio. Zeichen)
- IBM 4247-L03 nutzt Farbband 41U2235 (15 Mio. Zeichen)
- Modellbezeichnung steht auf dem Typenschild am Drucker

**To resolve:** Exakte Modellbezeichnung(en) aller IBM 4247 im Einsatz, plus Anzahl der Geräte.

### 2. Formulare und Durchschläge
⏱️ 10 min

Ob ein Umstieg auf Laser möglich ist, hängt davon ab, was auf den Nadeldruckern gedruckt wird. Laserdrucker können keine Durchschläge erzeugen.

- Welche Formulare werden aktuell auf den IBM 4247 gedruckt (Lieferscheine, Rechnungen, Werkstattaufträge, Etiketten)?
- Welche davon benötigen physische Durchschläge (Mehrfach-Kopien)?
- Gibt es regulatorische Anforderungen für physische Kopien in der Branche?
- Werden Endlosformulare (Traktor-Papier) verwendet oder Einzelblätter?

**To resolve:** Vollständige Liste aller Formulartypen mit Angabe, ob Durchschläge erforderlich sind — dies bestimmt den Migrationspfad.

### 3. IT-Infrastruktur und Software
⏱️ 5 min

Die ERP-/Software-Anbindung bestimmt, welche Ersatzdrucker kompatibel sind. Manche Laserdrucker emulieren IBM-Druckerprotokolle, andere nicht.

- Welches ERP-/Warenwirtschaftssystem ist im Einsatz (SAP, AS/400, anderes)?
- Wie kommuniziert die Software mit dem Drucker (IPDS, PCL, IBM Proprinter-Codes)?
- Monatliches Druckvolumen (ungefähr)
- Budget-Rahmen für eine eventuelle Umstellung

**To resolve:** ERP-System und Druckprotokoll identifizieren — dies bestimmt die Hardware-Kompatibilität bei einer Migration.

## Meeting Format

- **Typ:** Informationsgespräch / Bedarfserhebung
- **Erwartung:** Kunde hat Zugang zu den Druckern (Typenschild-Foto) und kennt die gedruckten Formulare
- **Ergebnis:** Ausgefüllte Checkliste → Ulrich kann konkreten Migrationspfad empfehlen

## Related

- **Issue:** [#1375](https://github.com/DaveX2001/deliverable-tracking/issues/1375)
- **Design Doc:** [IBM 4247 Druckerband-Beschaffung](design-doc-printer-research-1375)

🤖

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

