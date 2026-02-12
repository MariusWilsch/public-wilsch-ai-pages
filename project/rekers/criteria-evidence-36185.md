---
publish: true
---

# Kriteriennachweis — Projekt 36185: Lagergebäude GGM Gastro
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 36185 | Datum: 2019-12-05 | Wert: €3,9M

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 67 | 62 EML, 3 PDF, 2 ZIP |
| L2: EML-Anhänge | 86 | 59 PDF, 3 XLSX, 2 ZIP, 11 Pläne (ZIP-Inhalt), 11 sonstige |

Multiplikator: 2,3x (L0 vs. L0+L2)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Ochtrup, 48607, D | L1 |
| Kundenreferenz | ✅ CSV | 529 | L1 |
| Kalkulatorenwissen | ✅ CSV | 917 | L1 |
| Gebäudetyp | ✅ | Lagergebäude / Logistikzentrum (3 Lagerhallen Q-S) | L2 |
| Höhe | ✅ | 10,00 m (Vorstatik) / 14,50 m (Statik 2021, Gesamtgebäude) | L2 |
| Kran | ✅ | Ja — Autokrane für Montage | L2 |
| Dachlasten | ✅ | Schneelast sk=0,65 kN/m²; Wind WZ 2, qb,0=0,39 kN/m²; PV 0,15 kN/m² | L2 |
| Baustoff | ✅ | Spannbeton C50/60-F90 + Stahlbeton C35/45-F90 | L2 |
| Dachbegrünung | ❌ | Nicht gefunden | — |

**8/9 nachgewiesen** (3 strukturiert + 5 extrahiert). 1 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Ochtrup, 48607, D
- **Quelle:** Anfragen.csv, Zeile 3 → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 529
- **Quelle:** Anfragen.csv, Zeile 3 → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 917
- **Quelle:** Anfragen.csv, Zeile 3 → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Lagergebäude / Logistikzentrum — 3 Lagerhallen (Q, R, S) mit Brandwandtrennung, teilweise 2-geschossige Büro- und Technikeinbauten
- **Quelle:** `11167/_extracted/` → PDF: `2021.12.13 Statik.pdf` (Seite 5, Pos. 0)
- **Textstelle:** „Bei der geplanten Baumaßnahme handelt es sich um die Erweiterung eines Betriebsgeländes der Fa. GGM in Ochtrup in Form eines neuen Logistikzentrums. Das gesamte geplante Logistikzentrum gliedert sich in 3 Lagerhallen mit den Bezeichnungen Q - S, die jeweils durch Brandwände voneinander getrennt sind."
- **Bestätigt durch:** Grundriss `04_03 10.11.2021_EG` zeigt „Lagerhalle Q/R/S"; Ansichten `04_05` zeigen durchgehende Hallenkonstruktion; Vorstatik: „Errichtung eines Lagergebäudes für GGM Gastro"
- **Hinweis:** Nutzlastkategorie E (Lagerräume). In Halle Q und S 2-geschossige Büro-/Technikeinbauten.

### Höhe ✅
- **Wert:** H = 10,00 m (Vorstatik 2019, Standardmodul); H = 14,50 m (Statik 2021, Gesamtgebäude)
- **Quelle:** `11167/_extracted/2019.12.17 Vorstatik.pdf` (Seite 5) und `2021.12.13 Statik.pdf` (Seite 10)
- **Textstelle:** „Gebäudehöhe (Höhe Flachdach) H = 14.50 m" / „Bezugshöhe ze = 15.00 m"
- **Bestätigt durch:** Schnitte `04_04` zeigen Hallenquerschnitte mit Höhenkoten; Angebot 28388.11: Stützenlängen 14,55–15,75 m
- **Hinweis:** Diskrepanz durch Planungsweiterentwicklung — 2021er Statik umfasst Gesamtgebäude (246×145 m) statt Standardmodul (50×90,5 m). Maßgeblich: H = 14,50 m.

### Kran ✅
- **Wert:** Ja — Autokrane für Montage der Fertigteile; Schwerlastfahrzeuge erforderlich
- **Quelle:** `11167/_extracted/Angebot 28388.11.pdf` (Seite 2, Pos. 7.1 — Montage)
- **Textstelle:** „Für die Montage der Fertigteile setzen wir voraus, daß die Baustelle soweit befestigt ist, daß unser Kran unmittelbar am Einbauort aufgestellt werden kann und die Transportfahrzeuge bis in den Schwenkbereich des Krans fahren können."
- **Bestätigt durch:** `15846/_extracted/FreibleibendesAngebot 28388.50.pdf` (identischer Montageabschnitt 7.1)
- **Hinweis:** Kein spezifischer Krantyp/Tragfähigkeit — „Autokrane" als allgemeine Angabe. Detaillierte Kranlastangaben Rekers-intern.

### Dachlasten ✅
- **Wert:**
  - Schneelast: SLZ 1, sk = 0,65 kN/m² (Binnenland)
  - Windlast: WZ 2, qb,0 = 0,39 kN/m², qp = 0,77 kN/m² (Statik 2021)
  - Dachdecke ständig: 0,15 kN/m² (Dämmung + Abdichtung)
  - Installationen: 0,25 kN/m²
  - PV-Anlage: 0,15 kN/m² (optional)
- **Quelle:** `11167/_extracted/2019.12.17 Vorstatik.pdf` (Seite 4-5) und `2021.12.13 Statik.pdf` (Seite 9-10, 33)
- **Textstelle:** „Windzone WZ = 2 / Schneelastzone SLZ = 1 / char. Schneelast sk = 0.65 kN/m²"
- **Hinweis:** DIN EN 1990/1991. Geländehöhe in 2021 korrigiert auf 52,30 m (statt 64,00 m). Kein Dachbegrünung-Lastfeld vorhanden.

### Baustoff ✅
- **Wert:** Stahlbetonfertigteilbauweise — Spannbeton + Stahlbeton
  - Spannbeton-Satteldachbinder (BI1): C50/60-F90, L=24,00 m
  - Spannbeton-Parallelgurtbinder (BI2): C50/60-F90, L=12,00 m
  - Stb.-Stützen: C50/60-F90, b/d=60/60 cm
  - Stb.-Brandwände: C35/45, d=16 cm, F90
  - Stb.-Sandwichwände: C35/45-F90, d=36 cm mit EPS 035
  - Spannbetonhohlkörperdecke: h=20 cm
- **Quelle:** `11167/_extracted/Angebot 28388.11.pdf` (Seiten 3-15, Positionen 1.10-1.490)
- **Textstelle:** „Spannbeton-Satteldachbinder Stat. Pos. BI1 / L=24,00m / Höhe = ca. 88-124-88 cm / C50/60 - F90"
- **Bestätigt durch:** Statik 2021: „überwiegend in üblicher Stahlbetonfertigteilbauweise"; freibleibendes Angebot: 490 to Betonstahl + 39 to Spannstahl
- **Hinweis:** Durchgängig F90. C50/60 für tragende Hauptelemente, C35/45 für Wand-/Nebenelemente.

### Dachbegrünung ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebote (28388.1–28388.50), Vorstatik, Statik 2021, Schnitte, Ansichten durchsucht
- **Hinweis:** Dachflächenlasten nur Dämmung/Abdichtung, Installationen, PV. Kein Substrat oder Retentionslasten. Typische Industriedachlösung.

---

## Beobachtungen

- **Umfangreiche Angebotshistorie:** Dezember 2019 (Vorstatik, Angebot 28388.1) → Dezember 2021 (Angebot 28388.11 nach Statik) → Juli 2022 (28388.11b) → März 2023 (28388.50 an INPREBO B.V.). Angebotssumme 3.868.517,80 EUR konsistent.
- **Planungsweiterentwicklung:** Vorstatik (50×90,5 m, H=10 m) → Statik 2021 (246×145 m, H=14,5 m). Wesentliche Erweiterung des Planungsumfangs.
- **Internationaler Subunternehmer:** Angebot 28388.50 an INPREBO B.V. (Niederlande) — grenzüberschreitende Vergabe.
- **Massivbauweise:** 490 to Betonstahl + 39 to Spannstahl unterstreichen die Massivität.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/36185/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** b64012b8-aa19-47bc-87a3-866af679580a
