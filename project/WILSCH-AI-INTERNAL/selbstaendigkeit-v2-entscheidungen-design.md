---
publish: true
---

# Selbständigkeit v2 — Entscheidungsdokument
[[selbstaendigkeit-v2-entscheidungen-design]]

Aufbauend auf Design Doc v1 (Deutschland vs. Estland): Konkrete Entscheidungen zu Rechtsform, Geschäftskonto, Gründungszuschuss und Steuerservice nach Deep Research mit 20 Agents und 100+ Quellen.

---

## Problem Statement

Aufbauend auf Design Doc v1 (Deutschland vs. Estland) wurden drei offene Entscheidungsfelder durch Deep Research (20 Agents, 100+ Quellen) geklärt:

1. **Rechtsform:** Gewerbe bestätigt — UG nach Zahlenvergleich verworfen
2. **Geschäftskonto:** Wise Business gewählt
3. **Steuer-/Buchhaltungsservice:** Offene Entscheidung — Optionen recherchiert

**Preconditions:**
- Betriebsbedingte Kündigung durch Insolvenz steht bevor
- Einstiegs-Revenue <€30k, Ziel €60-100k+
- Auswanderung in 2-5 Jahren geplant
- Gründungszuschuss-Antrag steht unmittelbar bevor

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | David hat alle Entscheidungen dokumentiert und kann die Gründung Schritt für Schritt umsetzen |
| **Success** | Rechtsform, Konto, Gründungszuschuss-Ablauf und Steuerservice sind entschieden oder als offene Entscheidung mit Optionen dokumentiert |
| **Done test** | Kann David morgen beim Arbeitsamt mit vollem Kontext auftreten und danach die nächsten Schritte ohne weitere Research starten? → Ja → Done |

---

## Approach

### Part 1: Rechtsform — Gewerbe (Einzelunternehmen)

**Entscheidung:** Gewerbe. UG nach Zahlenvergleich verworfen.

**Warum nicht UG:**

| Kriterium | Gewerbe | UG |
|-----------|---------|-----|
| Gründung | 1 Tag, €20-60 | 3-6 Wochen, €570-1.010, Notar |
| Steuern bei €24k Gewinn | ~€1.500 | ~€11.000 (46%) |
| Buchhaltung | EÜR (simpel) | Bilanz (Steuerberater Pflicht) |
| Fixkosten/Jahr | ~€0-500 | ~€3.100-6.700 |
| Auflösung bei Auswanderung | 1 Tag, €0-26 | 14+ Monate, €2.300-6.600 |

UG hat **keinen Gewerbesteuer-Freibetrag** (€24.500 gilt nur für Personenunternehmen). Plus 25% Rücklagenpflicht bis €25k Stammkapital.

**Stiefvater-Input:** Empfahl UG als "das Einfachste" — bei höherem Revenue stimmt das, bei <€30k Einstieg ist Gewerbe objektiv einfacher und günstiger.

**Zukunftspfad:** Gewerbe jetzt → GmbH (nicht UG) wenn Gewinn dauerhaft >€60-80k.

**Haftung:** Berufshaftpflicht (Exali/Hiscox, ~€200-500/Jahr) deckt Vermögensschäden durch Softwarefehler ab — relevanter als UG-Haftungsbeschränkung bei reinem Service-Business.

---

### Part 2: Geschäftskonto — Wise Business

**Entscheidung:** Wise Business. Ein Konto für alles.

| Detail | Wert |
|--------|------|
| Kosten | €45 einmalig, €0/Monat |
| IBAN | BE (belgisch) — SEPA-konform |
| Währungen | 24 (EUR, USD, GBP, etc.) |
| EUR empfangen | Kostenlos (domestic) |
| Senden/FX | Ab 0,47% |
| Rechnungen | Ja (integriert) |
| Karte | Physisch €7, ATM 2× gratis/Monat |

**Revolut Business:** Ausgeschlossen — akzeptiert keine Gewerbetreibenden (nur HRB-Eintrag), LT-IBAN, keine DE-IBAN auch für Business.

**Rechtslage BE-IBAN:** EU-Verordnung 260/2012, Art. 9 verbietet IBAN-Diskriminierung. Kein Unternehmen und keine Behörde darf Zahlungen wegen des Landes der IBAN verweigern. Finanzamt MUSS auf BE-IBAN erstatten. Keine gesetzliche Pflicht für deutsches oder separates Geschäftskonto (GewO §1, HGB §238). Bei Weigerung: Widerspruch → BaFin-Beschwerde.

**ELSTER-Lastschrift:** Ggf. technische Blockade bei manchen Finanzämtern — Lösung: Steuervorauszahlungen per Überweisung von Wise.

**Alternativen (dokumentiert, nicht gewählt):**
- FYRST Base: €0/Monat, DE-IBAN, Deutsche Bank — schwach international
- Kontist Free: €0/Monat, DE-IBAN, Steuerrücklagen — nur für Freiberufler optimiert
- Qonto Solo: €9/Monat, DE-IBAN, DATEV — stärkstes DE-Paket

---

### Part 3: Gründungszuschuss (§93 SGB III)

**Förderung:** Bis ~€15.000-20.000 steuerfrei über 15 Monate.

| Phase | Dauer | Zahlung |
|-------|-------|---------|
| Phase 1 | 6 Monate | ALG I + €300/Monat |
| Phase 2 | 9 Monate | €300/Monat |

Einnahmen aus Selbständigkeit werden NICHT auf ALG I angerechnet.

**Kritische Reihenfolge:**

1. Arbeitsuchend melden (3 Tage nach Kenntnis der Kündigung)
2. ALG I beantragen und beziehen
3. Gründungsabsicht mitteilen → Antragsformulare abholen
4. Businessplan erstellen + Tragfähigkeitsbescheinigung holen
5. Gründungszuschuss-Antrag einreichen
6. **ERST NACH BEWILLIGUNG:** Gewerbe anmelden

⚠️ Gewerbe vorher anmelden = ALG I sofort weg = kein Gründungszuschuss.

**Businessplan:** 5-8 Seiten Textteil + Finanztabellen. Finanzplan in zwei Varianten (mit/ohne Zuschuss). Kostenlose Tools: IHK Unternehmenswerkstatt, Gründerplattform.de.

**Tragfähigkeitsbescheinigung:** AVGS-Gutschein bei der Arbeitsagentur beantragen = kostenloser Gründungscoach der Businessplan UND Tragfähigkeitsbescheinigung erstellt. Alternative: IHK (€0-120).

**Ermessen:** Kein Rechtsanspruch. Bewilligungsquote ~40-60%. Bei Ablehnung: Widerspruch innerhalb 1 Monat möglich.

**Insolvenz-Vorteil:** Betriebsbedingte Kündigung = keine Sperrzeit, voller ALG I-Anspruch. Keinen Aufhebungsvertrag unterschreiben.

**Referenz:** [Arbeitsagentur Gründungszuschuss](https://www.arbeitsagentur.de/arbeitslos-arbeit-finden/arbeitslosengeld/gruendungszuschuss-beantragen)

---

### Part 4: Steuer-/Buchhaltungsservice — Offene Entscheidung

**Anforderung:** Done-for-you Service wie Xolo für Marius — ein Mensch macht Buchhaltung und Steuern, meldet sich proaktiv wenn etwas fehlt.

**Self-Service-Tools (verworfen):** Accountable, lexoffice, Kontist Plus — alle erfordern Eigenarbeit. Kein persönlicher Ansprechpartner der alles erledigt.

**Done-for-you Optionen:**

| Option | Preis/Monat | Was sie machen |
|--------|-------------|---------------|
| Online-Steuerberater (Festpreis) | ~€80-150 | Buchhaltung, alle Steuererklärungen, persönlicher StB |
| Lokaler Steuerberater | ~€115-230 | Dasselbe + persönlicher Kontakt vor Ort |

**Was ein Steuerberater für Gewerbetreibende (€24-50k) übernimmt:**
- Laufende Buchhaltung (Belege verarbeiten)
- USt-Voranmeldungen (monatlich/quartalsweise)
- EÜR (Jahresabschluss)
- Einkommensteuererklärung
- Gewerbesteuererklärung
- Proaktive Hinweise bei fehlenden Belegen/Fristen

**Kosten-Schätzung:** ~€1.350-2.800/Jahr (€115-230/Monat). Selbst absetzbar als Betriebsausgabe.

**Undefined:** Welcher konkrete Steuerberater — Entscheidung nach Arbeitsamt-Termin. Online-StB vs. lokal klären.

---

### Part 5: Auswanderung & Gewerbeanmeldung

**Auswanderung mit Gewerbe:**
- Gewerbe 6-12 Monate VOR Wegzug abmelden → 1 Formular, €0-26, sofort wirksam
- Letzte Steuererklärung für das Jahr der Abmeldung einreichen
- Aufbewahrungspflicht: Belege 10 Jahre aufbewahren
- Im Ausland neue Firma nach dortigem Recht gründen
- Bei reinem Service-Business: keine/minimale Wegzugsbesteuerung

**Gewerbeanmeldung (wenn Gründungszuschuss bewilligt):**

| Schritt | Detail |
|---------|--------|
| Wo | Gewerbeamt (online in den meisten Bundesländern) |
| Kosten | €15-70 |
| Dokumente | Personalausweis + Formular GewA 1 |
| Tätigkeitsbeschreibung | "Entwicklung und Beratung von Software, insbesondere im Bereich künstliche Intelligenz und maschinelles Lernen; IT-Consulting" |
| Wirksam | Sofort am selben Tag |

**Danach:**
- Fragebogen zur steuerlichen Erfassung via ELSTER einreichen
- Regelbesteuerung wählen (nicht Kleinunternehmer — B2B-Kunden)
- Steuernummer kommt in 2-6 Wochen
- Rechnungen vorher mit Vermerk "Steuernummer beantragt" möglich
- Berufsgenossenschaft anmelden (innerhalb 1 Woche)
- Berufshaftpflicht abschließen (Exali/Hiscox, ~€200-500/Jahr)

---

## Source

- **Design Doc v1:** [Selbständigkeit DE vs Estland](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/selbstaendigkeit-deutschland-vs-estland-design)
- **Deep Research:** 20 Agents, 100+ Quellen (UG/Gewerbe, Wise/Revolut, Gründungszuschuss, Auswanderung, EU-IBAN Recht, All-in-One Steuerservices)
- **Transcript:** [2026-03-13](https://app.fireflies.ai/view/01KKKY8WVXETTZV9JZ5ASP64FP)
- **Session:** 8c0779d4-3ef2-4c13-86d5-4470f53ecbb5
- **Issue:** [#1138](https://github.com/DaveX2001/deliverable-tracking/issues/1138)
- **Rechtsgrundlage IBAN:** EU-Verordnung 260/2012, Art. 9 (IBAN-Diskriminierungsverbot)
- **Arbeitsagentur:** [Gründungszuschuss](https://www.arbeitsagentur.de/arbeitslos-arbeit-finden/arbeitslosengeld/gruendungszuschuss-beantragen)
