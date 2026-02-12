---
publish: true
---

# Criteria Evidence — Project 35764: Neubau Werkhalle van Eckendonk
[[client-rekers]]

Evidence for design doc Part 2. Per-criterion data availability with source files and extracted values.

Project: 35764 | Date: 2019-07-19 | Value: €491K

---

## Data Examined

| Layer | Count | Details |
|-------|-------|---------|
| L0: Folder files | 25 | 22 EML, 2 PDF, 1 ZIP |
| L2: EML attachments | 65 | 36 PDF, 14 JPG, 14 PNG, 1 other |
| _extracted (ZIP) | 13 | 10 PDF, 3 DWG |

Multiplier: 4.1x (L0 vs L0+L2+ZIP)

---

## Quick View

| Criterion | Status | Value | Source Level |
|-----------|--------|-------|-------------|
| Bauort | ✅ CSV | Gescher, 48712, D | L1 |
| Kundenreferenz | ✅ CSV | 108636 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1051 | L1 |
| Gebäudetyp | ✅ | Werkhalle (Hallengebäude) mit 2-gesch. Bürotrakt | L2 |
| Höhe | ✅ | 14,35 m (First); OK-Stütze +13,35 m; Hakenhöhe 8,70 m | L2 |
| Kran | ✅ | Ja — 2x ABUS ZLK 40t/10t + 2x optional 20t | L2 |
| Dachlasten | ✅ | Dachlast 0,45; Installation 0,15; PV 0,20 kN/m²; WLZ2; SLZ2 | L2 |
| Baustoff | ✅ | Spannbeton (Satteldachbinder); Stb.-Fertigteile; B500(A); ST1660/1860 | L2 |
| Dachbegrünung | ❌ | Not found | — |

**8/9 evidenced** (3 structured + 5 extracted). 1 absent.

---

## Structured Criteria (Anfragen.csv)

### Bauort
- **Value:** Gescher, 48712, D
- **Source:** Anfragen.csv, Row 2 → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Value:** 108636
- **Source:** Anfragen.csv, Row 2 → `KUNDE`

### Kalkulatorenwissen
- **Value:** 1051
- **Source:** Anfragen.csv, Row 2 → `KALKULATOR`

---

## Extracted Criteria (Project Files)

### Gebäudetyp ✅
- **Value:** Werkhalle (Hallengebäude) mit angebundenem 2-geschossigem Bürotrakt
- **Source:** `10593/_extracted/` → PDF: `19-02_2019-04-24_E-1000_Grundriss, Schnitt, Ansichten.pdf`
- **Passage:** Architectural concept plan (Konzeptstudie) by bb architekten shows "Werkhalle ~1850 m²" with attached "Sozialräume, Sekretariat, Büros ~150 m²". Title block reads "Neubau einer Werkhalle". The building has a main hall span of 30.00 m × 67.50 m with 50t crane runway and sectional doors.
- **Corroborated by:** `11239/wetransfer-69a6f9/Planunterlagen_extracted/19-02_BA-1000d_Grundrisse+Schnitt.pdf` (Bauantragsplanung M. 1:100) shows "Werkhalle A= 1984.66 m²" with rooms for Büro, Besprechung, Umkleide, Aufenthalt, Kompressor, HA/Technik. Section 1.1.2 of Angebot 28273.30 is explicitly titled "Bürotrakt: 2-geschossig".
- **Note:** Building type is unambiguously an industrial workshop hall (Hallengebäude / Werkhalle) with integrated 2-storey office wing. The quotation separates pricing into "Hallenkonstruktion" and "Bürotrakt: 2-geschossig".

### Höhe ✅
- **Value:** Firsthöhe +14,35 m (building permit plans); OK-Stütze ca. +13,35 m; Unterkante Binder ca. +12,35 m; Hakenhöhe 8,70 m; Bürotrakt +7,25 m
- **Source:** `11239/wetransfer-69a6f9/Planunterlagen_extracted/19-02_BA-3000d_Ansichten.pdf`
- **Passage:** All four elevations (Westen, Norden, Osten, Süden) show consistent height markings: ground level ±0.00, hall ridge at +14.35, office section at +7.25. The section drawing in BA-1000d confirms: "14.35" total height with "14.05" to underside of roof, "+12.34" at Unterkante Binder, and "+0.30" at Sockelelement OK.
- **Corroborated by:** Konzeptstudie E-1000 (earlier version) shows +14.30 m; Angebot 28273.30 Pos. 1.1.1.1.01 states "Unterkante Binder: ca. +12,35 m" and "OK-Stütze = ca. +13,35 m"; ABUS crane data sheet shows HG = 12340 mm and HS = 10390 mm hall section dimensions.
- **Note:** The early concept (E-1000, April 2019) shows 14.30 m; the building permit plans (BA-1000d/BA-3000d, Feb 2020) refine this to 14.35 m. The quotation column heights confirm OK-Stütze at +13,35 m. Hakenhöhe 8,70 m is stated in the concept plan section drawing.

### Kran ✅
- **Value:** Ja. 2x ABUS ZLK 40t/10t (Zweiträger-Laufkran, Tandembetrieb, max. Gesamttraglast 50t je Kranbahn); plus 2x optional 20t Kranbahnen
- **Source:** `11239/_extracted/19-02_2020-03-02_Krandatenblätter.pdf` (ABUS Kransysteme GmbH data sheets)
- **Passage:** 6-page ABUS technical document, Auftrag 16949810, dated 02.03.2020. Shows: "Kran: ZLK 40 t / 10 t x 28250 mm", "Berechnungsgrundlage: DIN 15018, H2/B3", "Gesamtgewicht: 29960 kg", "Kranfahren: 10/40 m/min", "Betriebsort: Hallenbetrieb", Kranklassifizierung S2. Lageplan shows two crane bays (Pos. 1 and Pos. 2) each with ZLK 40t/10t in Tandembetrieb (Master-Master). Hall section A-A shows span 28250 mm, HG=12340, HS=10390.
- **Corroborated by:**
  - `11239/_extracted/19-02_2019-11-29_Anlage 1_optional 20t Kräne.pdf`: Floor plan with red handwritten annotations "optional 20t Kran" showing two additional 20t crane runs in the hall, dated 29.11.19.
  - Angebot 28273.20 (crane surcharge quotation): "Kosten für den Einsatz der Spezialkrane", "max. 2 Kranbahnen mit einer max. gesamten Traglast von je 50 to (40 to + 10to)", "Zusätzlich sind die Lasten aus 2 optionalen Kranbahnen mit einer max. Traglast von je 20 to" — total crane surcharge €95.715,80 netto.
  - Angebot 28273.11 Vorbemerkungen: "wir gehen von max. 2 Kranbahnen mit einer max. gesamten Traglast von je 50 to (40 to + 10to) aus."
- **Note:** Crane data is exceptionally well documented with full ABUS technical specifications including wheel loads (Radlasten), side loads (Seitenlasten), and dynamic factors per DIN EN 1991-3. The Kranbahnstützen are designed with expansion option (Erweiterungsoption) in axes 4-10/B (Angebot 28273.11) or 5-10/B (Angebot 28273.30).

### Dachlasten ✅
- **Value:**
  - Dachlast = 0,45 kN/m² (Trapezblech, Dämmung, Abdichtung)
  - Installation = 0,15 kN/m²
  - PV = 0,20 kN/m²
  - Wind: Windlastzone WZ2 (WLZ2)
  - Schnee: Schneelastzone 2 (SLZ2)
  - Dachtrapezbleche als 3- bzw. 4-Feldträger, Durchlauffaktor = 1,10
- **Source:** `11239/Angebot Nr. 28273.30.pdf`, Seite 4 (Pos. 1.1.1.1.01 Spannbeton-Satteldachbinder)
- **Passage:** Under "Lasten:" the quotation explicitly lists: "Dachlast = 0,45 kN/m² (Trapezbl., Dä., Abkl.)", "Installation = 0,15 kN/m²", "PV = 0,20 kN/m²", "Wind: WLZ2", "Schnee: SLZ2"
- **Corroborated by:**
  - Angebot 28273.30 Vorbemerkungen (Seite 1): "Wind- u. Schneelasten gemäß DIN EN 1991: Windlastzone WZ2, Schneelastzone 2"
  - Angebot 28273.0 (Richtpreis-Angebot, Aug 2019), Pos. 1.01: identical load values (Dachlast 0,45; Installation 0,15; PV 0,20; WLZ2; SLZ2)
  - Angebot 28273.11 Vorbemerkungen: same wind/snow zones confirmed
  - Bauantragsplanung BA-1000d section drawing shows "Dachaufbau Werkhalle: Foliendachabdichtung, Dämmung, Dampfsperre kalt selbstklebend, Trapezblech"
- **Note:** All three quotation versions (28273.0, 28273.11, 28273.30) state identical roof load values. PV loading of 0.20 kN/m² is explicitly included. Erdbebenzone: "Der Bauort befindet sich nicht in einer Erdbebenzone." No additional loads beyond those listed. Sohlbelastung im Bereich der Lagerflächen = 50,0 kN/m² (floor, not roof).

### Baustoff ✅
- **Value:** Spannbeton-Satteldachbinder (prestressed concrete saddle roof beams, l=30,00 m); Stahlbeton-Fertigteile (precast reinforced concrete); Betonstahl B500(A); Spannstahl ST1660/1860 nach DIN 488; Beton nach DIN EN 206-1/DIN 1045-2 mit Portlandzement CEM I; Druckfestigkeitsklasse/Betongüte gemäß statischer Erfordernis
- **Source:** `11239/Angebot Nr. 28273.30.pdf`, Vorbemerkungen Seite 2 (Beton, Bewehrung sections)
- **Passage:**
  - Beton: "Die Ausführung der Betonarbeiten erfolgt nach DIN EN 206-1/ DIN 1045-2 unter Verwendung von Portlandzement CEM I. Druckfestigkeitsklasse/Betongüte gemäß statischer Erfordernis."
  - Bewehrung: "Betonstahl B500(A), Spannstahl ST1660/1860 nach DIN 488."
  - Pos. 1.1.1.1.01: "Spannbeton-Satteldachbinder, l = 30,00 m" with "Binderhöhe: 1,45 m im First"
  - Pos. 1.1.2.09: "Spannbeton-Hohlkörperdecken EG und OG, d = 20 cm"
  - Feuerwiderstandsklassen: Binder/Riegel/Abfangträger F90; Stützen/Unterzüge/Deckenplatten F90; Brandwandplatten F90
- **Corroborated by:** Angebot 28273.0 and 28273.11 contain identical Beton and Bewehrung specifications. The cover letter for all versions describes "Herstellung, Lieferung und Montage von Stb.-Fertigteilen" (manufacture, delivery and assembly of reinforced concrete precast elements).
- **Note:** Concrete grade is specified as "gemäß statischer Erfordernis" (per structural requirement) rather than a specific grade like C50/60. The prestressing steel (Spannstahl ST1660/1860) confirms the roof binders are prestressed concrete (Spannbeton), not ordinary reinforced concrete. The full letter heading identifies REKERS Betonwerk GmbH & Co. KG as the precast concrete manufacturer.

### Dachbegrünung ❌
- **Value:** Not found
- **Source:** Searched all quotation versions (28273.0, 28273.11, 28273.20, 28273.30), architectural plans (E-1000 concept, BA-1000d Bauantragsplanung, BA-3000d Ansichten), and the Anlage 1 crane document. No mention of green roof (Dachbegrünung, Gründach, extensive/intensive Begrünung, or additional roof loads beyond Dachlast/Installation/PV/Wind/Schnee).
- **Note:** The roof build-up described in BA-1000d is: "Foliendachabdichtung, Dämmung, Dampfsperre kalt selbstklebend, Trapezblech" — a standard industrial flat roof without vegetation layer. The Dachlasten enumeration in the quotation is exhaustive (0,45 + 0,15 + 0,20 = 0,80 kN/m² total dead load plus climate loads) and does not include any green roof loading. This is consistent with the industrial Werkhalle building type where green roofs are uncommon.

---

## Observation

This project has exceptionally rich technical documentation across all file layers. The quotation evolved through four versions (28273.0 Richtpreis Aug 2019 → 28273.11 Feb 2020 → 28273.12 Nachtrag May 2020 → 28273.30 final consolidated Jun 2020), each with consistent structural parameters. The ABUS crane data sheets are unusually detailed, providing full DIN EN 1991-3 load specifications with dynamic factors — this is uncommon in typical project folders and represents high-value engineering data.

The building location shifted between quotation versions: the Richtpreis (28273.0) references "46342 Velen" while all later versions reference "48712 Gescher" (Marie-Curie-Straße 20). The Anfragen.csv captures the final Gescher location.

The Kranbahnanlage is a significant cost item (€95.715,80 netto surcharge in Angebot 28273.20) on top of the base hall construction (€228.544,60 in Angebot 28273.30), representing approximately 42% of the hall structure cost. The optional 20t crane lines add further structural requirements to the columns with expansion provisions built into the Kranbahnstützen design.

One criterion (Dachbegrünung) is definitively absent — the standard industrial roof specification with trapezoid sheet metal and membrane waterproofing excludes any green roof layer, and this is confirmed by the complete absence of any related load values across all documents.

---

## Source

- **Data location:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/35764/`
- **Design doc:** [Feasibility Report, Part 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** b64012b8-aa19-47bc-87a3-866af679580a
