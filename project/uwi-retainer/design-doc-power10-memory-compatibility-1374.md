---
publish: true
---

# Design Doc: Power10 Speicherkompatibilität — EMRF/EM7F vs EM78 für 9105-42A
[[client-02_uwi]]

Kompatibilitätsmatrix: Können L1024-Speicher-Feature-Codes (EM7F, EMRF) den S1024-Feature-Code EM78 auf dem IBM Power10 9105-42A ersetzen — getrieben durch eine günstigere Beschaffungsmöglichkeit.

---

## Problemstellung

Der IBM Power10 S1024 (9105-42A) benötigt Feature-Code EM78 für seine 256 GB DDR4 DDIMMs — bei ca. $60.000 pro Paar. Ein Kunde aus Ulrichs Netzwerk hat EM7F-gelabelte Module zu einem niedrigeren Preis verfügbar. EM7F ist offiziell für den Power L1024 (9786-42H) vorgesehen, nicht den S1024. Die Frage: Ist das eine harte technische Einschränkung oder nur ein IBM-Bestelllabel auf derselben physischen Hardware?

Eine parallele Frage besteht für EMRF — ein weiterer L1024 Feature-Code für dieselbe DIMM-Größe. Wenn beide intern identische Hardware verwenden, ist die Beschaffungsbeschränkung kommerziell, nicht technisch.

**Voraussetzungen:**
- Zielsystem ist ein Power10 S1024 (9105-42A) mit 4U DDIMM-Slots
- EM78 ist der einzige 256 GB DDR4 Feature-Code den IBM für 9105-42A listet (Tabelle 1, IBM Plugging Rules)
- EM7F und EMRF sind nur für 9786-42H gelistet (Tabelle 3, IBM Plugging Rules)
- Ulrichs Kunde kann EM7F-Module günstiger liefern

---

## Erfolgsdefinition

| Element | Definition |
|---------|-----------|
| **Ziel** | Ulrich kann EM7F (oder EMRF) Module für ein 9105-42A System sicher beschaffen — mit Kenntnis der technischen Kompatibilität, Garantie-Implikationen und Kostenersparnis |
| **Erfolg** | Ulrich liest dieses Dokument und kann die Frage seines Kunden definitiv beantworten: "Ja, diese DIMMs funktionieren im S1024" oder "Nein, hier ist der Grund" — ohne IBM anrufen zu müssen |
| **Done-Test** | Beantwortet die Kompatibilitätsmatrix beide Kernfragen? (1) Können EM7F/EMRF den EM78 auf 9105-42A physisch ersetzen? (2) Was sind CCIN und Teilenummer für EMRF? |

---

## Ansatz

### Teil 1: Die "Technische Nummer" — CCIN 32AE

Jedes IBM DDIMM trägt eine CCIN (Custom Card Identification Number) — die Hardware-Kennung die das System beim Booten liest. Feature-Codes wie EM78 oder EM7F sind Bestellbezeichnungen, keine Hardware-Kennungen. Das System sieht die CCIN, nicht den Feature-Code.

Alle drei Feature-Codes verweisen auf dasselbe physische Modul:

| Kennung | Wert |
|---------|------|
| **CCIN** | 32AE |
| **Teilenummer (FRU)** | 01GY925 |
| **Kapazität pro DIMM** | 256 GB |
| **Typ** | DDR4 |
| **Geschwindigkeit** | 2933 MHz |
| **Dichte** | 16 Gbit |
| **Höhe** | 4U |

Midland Information Systems listet EM78 und EM7F als ein einziges Produkt unter einer URL — gleiche CCIN, gleiche Teilenummer. DataTech listet dieselbe 01GY925 / 32AE für EM78 auf dem E1050 (9043-MRX). Midland listet auch EMC4 (die E1080-Variante) mit derselben 32AE / 01GY925 in einer 4-DIMM-Konfiguration.

Das ist kein Einzelfall. IBM verwendet verschiedene Feature-Codes für identische Hardware über Systemmodelle hinweg — dasselbe Muster gilt für PCIe-Adapter (EN2L/EN2M teilen CCIN 2CFC, EJ2B/EJ2C teilen CCIN 57F2).

### Teil 2: Feature-Code Aufschlüsselung — EM78 vs EM7F vs EMRF

Die drei Feature-Codes unterscheiden sich nur im IBM-Bestellkontext — Zielsystem und Paketgröße:

| | EM78 | EM7F | EMRF |
|---|---|---|---|
| **Zielsystem** | S1024 (9105-42A) | L1024 (9786-42H) | L1024 (9786-42H) |
| **DIMMs pro Feature** | 2 (Standard-Paar) | 2 (Standard-Paar) | 4 (SAP-Paket) |
| **Gesamt pro Feature** | 512 GB | 512 GB | 1024 GB |
| **Kapazität pro DIMM** | 256 GB | 256 GB | 256 GB |
| **Höhe** | 4U | 4U | 4U |
| **CCIN** | 32AE | 32AE | 32AE |
| **Teilenr. (FRU)** | 01GY925 | 01GY925 | 01GY925 |
| **Bestellregeln** | Standard | Standard | SAP-Programm, min 2 TB |

EMRF gehört zu IBMs SAP-Speicherpaket-Linie (EMRX für 64 GB, EMRV für 128 GB, EMRF für 256 GB). Diese Pakete haben andere Bestellbedingungen — Minimum 2 TB gesamt, nur L1024 — aber die einzelnen DIMMs darin sind physisch identisch mit den Standard EM7F- und EM78-DIMMs.

Der S1024 und L1024 sind beide 4U-Gehäuse, es gibt also keine Formfaktor-Inkompatibilität zwischen ihren DDIMMs.

### Teil 3: Kompatibilitäts-Ergebnis

**F1: Können EM7F oder EMRF den EM78 auf 9105-42A physisch/elektrisch ersetzen?**

**Ja.** Die einzelnen DIMMs sind identische Hardware — gleiche CCIN (32AE), gleiche FRU-Teilenummer (01GY925), gleiche DDR4 256 GB 2933 MHz Spezifikation, gleicher 4U Formfaktor. Der S1024 identifiziert Speicher beim Booten über die CCIN, nicht über den Feature-Code auf der Bestellung. In IBM-Dokumentation und Community-Foren wurde keine Feature-Code-Validierung auf Firmware-Ebene gefunden.

Belege:
- Midland listet EM78 + EM7F unter einem Produkteintrag (geteilte CCIN)
- IBM verwendet dieselbe CCIN/FRU über 4+ Feature-Codes (EM78, EM7F, EMC4, EMRF) auf 4 verschiedenen Systemmodellen
- DDR4 DDIMMs von Power10-Systemen sind vorwärtskompatibel mit Power11 über dasselbe OMI-Interface — bestätigt systemübergreifende Hardware-Identität

**F2: Was sind CCIN und Teilenummer für EMRF?**

Jedes einzelne DIMM in einem EMRF-Paket trägt **CCIN 32AE** und **Teilenr. 01GY925** — identisch mit EM78 und EM7F. EMRF ist ein 4-DIMM SAP-Bestellpaket, kein eigenständiges Hardwareteil. Es hat keine eigene CCIN.

**Garantie-Hinweis:** Da CCIN und FRU identisch sind, identifizieren IBM-Support-Tools das DIMM unabhängig davon, welcher Feature-Code auf der ursprünglichen Bestellung stand. Ein Support-Techniker der Diagnosen auf einem 9105-42A durchführt, sieht "CCIN 32AE / 01GY925" — nicht unterscheidbar von einem über EM78 gekauften Modul.

---

## Quelle

- **IBM Dokumentation:** [Plugging Rules für Speichermodule — 9105-41B, 9105-42A, 9786-42H](https://www.ibm.com/docs/en/power10/9105-41B?topic=modules-plugging-rules-memory-in-system) (Tabellen 1 und 3)
- **Händler — DataTech:** [EM78 512GB DDR4 Listing](https://datatechcomputer.com/memory/ibm-em78-512gb-Power10-S1024-9105-42A-ddr4-memory/) (CCIN 32AE, Teilenr. 01GY925)
- **Händler — Midland:** [32AE EM78/EM7F 512GB Listing](https://www.midlandinfosys.com/ibm-32ae-01gy925-em78-em7f-512gb-ddr4-memory-2x256gb-ddimms) (gemeinsame Produktseite)
- **Händler — Midland:** [32AE EMC4 1TB Listing](https://midlandinfosys.com/ibm-32ae-emc4-01gy925-1tb-ddr4-memory-9080-hex-e1080) (gleiche CCIN auf E1080)
- **IBM Announcement:** [Enhanced Power10 capabilities (ENUS123-017)](https://www.ibm.com/common/ssi/cgi-bin/ssialias?infotype=an&subtype=ca&appname=gpateam&supplier=897&letternum=ENUS123-017) (EMRF/EMRV SAP-Paket Spezifikationen)
- **Generationskompatibilität:** [IT Jungle — IBM Preserves Memory Investments](https://www.itjungle.com/2025/05/21/ibm-preserves-memory-investments-across-power10-and-power11/)
- **Session:** `5cabfe4e-32ae-4652-9811-458fbd5c211d`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

