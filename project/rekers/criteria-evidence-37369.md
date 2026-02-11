---
publish: true
---

# Criteria Evidence — Project 37369: Kühl- und Lagerhalle Schütte
[[client-rekers]]

Evidence for design doc Part 2. Per-criterion data availability with source files and extracted values.

Project: 37369 | Date: 2020-12-02 | Value: €1.98M | Files: 19 EML (containing 24 PDF attachments)

---

## Quick View

| Criterion | Status | Value |
|-----------|--------|-------|
| Bauort | ✅ CSV | Wietmarschen Lohne, D |
| Kundenreferenz | ✅ CSV | 108636 |
| Kalkulatorenwissen | ✅ CSV | 1051 |
| Gebäudetyp | ✅ | Kühlhalle + Lagerhalle + Verladeeinheit |
| Höhe | ✅ | 16m / 12m / 6m (by zone) |
| Kran | ❌ | Not found |
| Dachlasten | ✅ | Wind/Schnee gemäß DIN, PV 0.20 kN/m² |
| Baustoff | ✅ | Spannbeton C 50/60 |
| Dachbegrünung | ❌ | Not found |

**7/9 evidenced** (3 structured + 4 extracted). 2 confirmed absent.

---

## Structured Criteria (Anfragen.csv)

### Bauort
- **Value:** Wietmarschen Lohne, D (PLZ not recorded)
- **Source:** Anfragen.csv, Row 23 → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Value:** 108636
- **Source:** Anfragen.csv, Row 23 → `KUNDE`

### Kalkulatorenwissen
- **Value:** 1051
- **Source:** Anfragen.csv, Row 23 → `KALKULATOR`

---

## Extracted Criteria (Project Files)

### Gebäudetyp ✅
- **Value:** Kühlhalle + Lagerhalle + Verladeeinheit + Büroeinheit
- **Source:** `12687/` → EML attachment: `20-43_Schütte_Neubau Kühl- und Lagerhallen_11-2020.pdf`
- **Passage:** 3D architectural rendering showing labeled building zones:
  - Kühlhalle 1-2, je 4.500m²
  - Lagerhalle 1-4, je 4.500m²
  - Erweiterung 1-3, je 4.500m²
  - Verladeeinheit, je 2.230m²
  - Büroeinheit 2-Geschossig
  _(Multi-zone logistics complex: cooling halls, storage halls, expansion areas, loading unit, and 2-story office)_

### Höhe ✅
- **Value:** Kühlhalle 16m, Lagerhalle 12m, Verladeeinheit 6m
- **Source:** `12687/` → EML attachment: `20-43_Schütte_Neubau Kühl- und Lagerhallen_11-2020.pdf`
- **Passage:** Labels on 3D rendering:
  - "Kühlhalle 1-2 — Höhe 16m"
  - "Lagerhalle 1-4 — Höhe 12m"
  - "Erweiterung 1-3 — Höhe 12m"
  - "Verladeeinheit — Höhe 6m"
  _(Three distinct height zones: 16m for cold storage, 12m for ambient logistics, 6m for truck loading docks)_
- **Note:** Height information only visible as text labels on a 3D rendering image — not extractable via text parsing.

### Kran ❌
- **Value:** Not found
- **Source:** All 24 PDFs searched (architectural plans, structural plans, offers, contracts)
- **Note:** No mentions of Kran, Laufkran, Hallenkran, Kranbahnanlage. No crane rails in structural plan R1. Building designed for ground-based logistics (forklifts, loading docks).

### Dachlasten ✅
- **Value:** Wind- u. Schneelast gemäß DIN; PV 0,20 kN/m²; Trapezblech/Dämmung/Abklebung 0,45 kN/m²; Installation 0,10 kN/m²
- **Source:** `12687/` → EML attachment: `Angebot Nr. 28687.1.pdf`, page 3 (Seite 1 von 13)
- **Passage:**
  > Grundlage:
  > Folgende Annahmen sind Grundlage des Angebots:
  > • Dachbelastung: Wind- u. Schneelast gemäß DIN
  > • Photovoltaikanlage = 0,20 kN/m²
  > • Trapezblech, Dämmung, Abklebung = 0,45 kN/m²
  > • Installation = 0,10 kN/m² (in Teilbereichen Last aus einer abgehängten Decken g < 0,15 kN/m²)
  > • Dachtrapezbleche werden als 3- bzw. 4-Feldträger ausgeführt
  > • Expositionsklasse der Spannbinder XC1
  _(Roof designed for DIN wind/snow loads + PV panels + construction materials. Specific load values per square meter.)_

### Baustoff ✅
- **Value:** Spannbeton-Fertigteile, C 50/60
- **Source:** `12687/` → EML attachment: `Angebot Nr. 28687.1.pdf`, page 3
- **Passage:**
  > 1.01 Spannbeton.-Satteldachbinder L = 40,00 m (Achsmaß)
  > in C 50/60, Querschnitt: Doppel-I-Profil
  > UK Binder + 9,60 m
  > 1,15-1,75-1,15 m hoch.
  _(Prestressed concrete saddle roof girders, 40m span, high-strength C 50/60 concrete, double-I cross-section)_
- **Additional source:** Contract header (`20-43_Bauvertrag zur Unterschrift.pdf`): "Gewerk: Stahlbeton Fertigteile"

### Dachbegrünung ❌
- **Value:** Not found
- **Source:** All documents searched
- **Note:** Roof designed as standard industrial roof with 3% slope, PV installation, trapezoidal metal sheet. No mention of green roof substrate, drainage layers, or vegetation loads.

---

## Methodology Note

This project was initially classified as "EML-only, 2/6 criteria" based on text parsing. Re-analysis using PDF-as-image reading revealed **24 PDF attachments embedded inside EML files** (architectural drawings, REKERS quotations, contracts, soil reports). Reading these as images yielded **4/6 extraction criteria** — the same rate as projects with standalone PDFs.

**Key insight:** EML files are containers. Text parsing sees the email body; image reading sees the full document content including embedded technical drawings, specifications, and labeled renderings.

---

## Source

- **Data location:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/37369/`
- **Extracted attachments:** `/tmp/rekers-37369/` (24 PDFs, 17 images)
- **Design doc:** [Feasibility Report, Part 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** cb7381c0 (Part 1 extraction pass)
