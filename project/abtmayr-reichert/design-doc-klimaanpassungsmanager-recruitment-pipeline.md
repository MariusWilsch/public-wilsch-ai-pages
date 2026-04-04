---
publish: true
---

# Design Doc: Rekrutierungs-Pipeline Klimaanpassungsmanager


Rekrutierungs-Pipeline für Klimafolgenschutz e.V.: Wie der Verein Kommunen mit Werkstudenten als Klimaanpassungsmanager verbindet — auf Basis bundesweiter Methodik-Rahmenwerke.

---

## Problemstellung

Seit Juli 2024 verpflichtet das Klimaanpassungsgesetz (KAnG) jede Kommune zur Erstellung eines Klimaanpassungskonzepts. Die Personallücke ist gravierend: Bundesweit sind nur ~300 Klimaanpassungsmanager (KAM) beschäftigt — im Vergleich zu ~2.500 Klimaschutzmanagern. Der primäre Bundesfördermechanismus (DAS) hat sein Budget seit August 2025 ausgeschöpft; künftige Förderfenster sind geplant, aber nicht terminiert.

Kommunen stehen vor einem dreifachen Problem: gesetzliche Verpflichtung, fehlendes Personal, begrenzte Finanzierung. Der professionelle KAM-Markt kann nicht schnell genug skalieren — formale Stellen erfordern abgeschlossene Studiengänge und konkurrieren auf TVöD EG 10–11 Gehaltsniveau (~3.500–4.500 €/Monat).

Klimafolgenschutz e.V. adressiert diese Lücke durch die Schaffung einer neuen Stellenkategorie: Werkstudenten als Klimaanpassungsmanager. Der e.V. rekrutiert Studierende, stellt den methodischen Rahmen bereit (abgeleitet von PIK/UBA-Tools) und verbindet sie mit Kommunen — Angebot und Nachfrage gleichzeitig.

Das Modell folgt dem Prinzip, Angebot und Nachfrage parallel aufzubauen — Studierende rekrutieren und gleichzeitig Kommunen als Einsatzorte gewinnen.

---

## Erfolgsdefinition

| Element | Definition |
|---------|-----------|
| **Ziel** | Klimafolgenschutz e.V. betreibt eine Plattform, die Werkstudenten als Klimaanpassungsmanager mit Kommunen verbindet — Angebot und Nachfrage parallel skalierend |
| **Erfolg** | 100 aktive Werkstudenten und 200 teilnehmende Gemeinden zum 1. September 2026 |
| **Done-Test** | Kann ein offenes Design-Thema formuliert werden, das eine Meeting-Agenda braucht? → Wenn NEIN → Design ist vollständig |

---

## Rahmenbedingungen

### Team

| Person | Rolle | Verantwortung |
|--------|-------|---------------|
| **Michael Reichert** | Externe Beziehungen + Gemeinden-Netzwerk | BAFA/PEK-Kontakte, Gemeinden-Akquise über bestehende Netzwerke, Fördermittelklärung |
| **Ralph Loser** | Geschäftsführer + Organisation | Meilensteinplanung, NDA-Vorlagen, Events (Bachelormesse), operative Koordination |
| **Wilsch AI** | Technische Umsetzung + Content | LinkedIn-Content, Design Docs, Plattform-Infrastruktur, GitHub-Tooling |
| **David Eberle** | Entwicklung + Planung | Technische Entwicklung, Sales-Channel-Konzeption, Matching-System-Design |

### IP-Eigentum

Geistiges Eigentum aus der gemeinsamen Arbeit gehört dem Klimafolgenschutz e.V. Die genaue Ausgestaltung wird über eine Loyalitätsvereinbarung (NDA) geregelt.

### Governance

- **Wöchentliche Meetings:** Dienstags 13:00 Uhr
- **Entscheidungsstruktur:** Michael (externe Beziehungen) + Ralph (operative Leitung) + Marius (technische Umsetzung)

---

## Vorgehen

Das Projekt gliedert sich in drei Meilensteine. Meilenstein 1 und 2 laufen parallel — Angebot (Werkstudenten) und Nachfrage (Gemeinden) müssen gleichzeitig aufgebaut werden.

```
M1: Werkstudenten-Pipeline          ─── parallel ───          M2: Gemeinden-Akquise
 (Agenda, Stellenbeschreibung,                                  (Sales Channels, Funnel,
  LinkedIn, CTA, Screening)                                      Mitgliedschaft, Bedarfsmeldung)
              ↘                                               ↙
                    M3: KI-Matching + Projektabwicklung
                            (zurückgestellt)
```

### Meilenstein 1: Werkstudenten-Pipeline

Die bestehende Pipeline — von der Agenda über die Stellenbeschreibung bis zur Website-Integration — bildet den Angebotsaufbau. Ergänzt um Screening-Mechanismen und physische Akquise-Kanäle.

#### Rekrutierungs-Funnel (End-to-End)

**Ungeklärt:** Der Gesamtfluss von Erstkontakt bis Einsatz ist nicht formalisiert. Die einzelnen Komponenten (Agenda, Stellenbeschreibung, LinkedIn, CTA, Screening) sind definiert, aber der durchgängige Funnel — mit Entscheidungspunkten, Konversionsannahmen und Übergaben — fehlt. → Meeting Agenda

Konzeptionelle Stufen:
```
Sichtbarkeit (LinkedIn/Messe/CTA) → Bewerbung → Screening → Matching → Einsatz in Kommune
```

#### Agenda (Klimalotse 5-Modul-Rahmenwerk)

Ein Dokument mit doppeltem Zweck: Es zeigt Kandidaten, was sie im Einsatz tun werden, und gibt Kommunen Vertrauen in die Methodik. Abgeleitet vom bundesweiten Klimalotse 3.0 Rahmenwerk (UBA), nicht von Grund auf neu erfunden.

**Struktur: Klimalotse 5-Modul-Rahmenwerk**

Die Agenda präsentiert den Klimalotse-Lebenszyklus, angepasst auf den Werkstudenten-Scope. Jedes Modul beschreibt den übergreifenden kommunalen Prozess und den spezifischen Beitrag der Studierenden:

| Modul | Kommunaler Prozess | Beitrag der Studierenden |
|-------|-------------------|-------------------------|
| **1. Orientierung** | Politisches Mandat sichern, KAM benennen, Governance etablieren | Stakeholder-Landschaft kartieren, vergangene Wetterereignisse recherchieren, GERICS-Klimaprojektionen zusammenstellen, UBA Anpassungsscanner durchführen |
| **2. Klimarisikoanalyse** | Formale Risikobewertung nach DIN EN ISO 14091, Stakeholder-Workshops | Wirkungsketten entwickeln, Empfindlichkeits-/Expositionsdaten erheben, Risikomatrizen vorausfüllen, Workshop-Ergebnisse dokumentieren |
| **3. Maßnahmenentwicklung** | Anpassungsziele definieren, Maßnahmen auswählen und priorisieren | UBA Tatenbank auswerten, Maßnahmensteckbriefe erstellen, Förderprogramme recherchieren, Anträge unterstützen |
| **4. Strategiedokument** | Klimaanpassungsstrategie veröffentlichen, Ratsbeschluss einholen | Redaktionelle Unterstützung, Monitoring-Rahmenwerk aufbauen, erste Maßnahmen koordinieren |
| **5. Monitoring** | Umsetzung verfolgen, Ergebnisse evaluieren | Indikatorsets pflegen, Fortschrittsberichte erstellen |

**Schwerpunktbereiche:** Gewässerschutz, Hitzeschutz, Bepflanzung, Innenstadtgestaltung — typische kommunale Bedarfe, keine Einschränkung des Rahmenwerks.

**Tools, die Studierende nutzen:**

| Tool | Zweck | Zugang |
|------|-------|--------|
| [Klimalotse 3.0](https://www.umweltbundesamt.de/node/33047) | Schritt-für-Schritt Anpassungsmethodik | Kostenlos, herunterladbar |
| [GERICS Klimaausblicke](https://www.gerics.de/products_and_publications/fact_sheets/landkreise/index.php.de) | 17 Klimaindikatoren pro Landkreis, 3 RCP-Szenarien | Kostenlos, 401 Landkreise |
| [UBA Anpassungsscanner](https://www.umweltbundesamt.de/node/118272) | Excel-Selbstbewertung über 5 Dimensionen | Kostenlos (seit Sep 2025) |
| [KLiVO Portal](https://www.klivoportal.de) | Meta-Tool, das alle Bundestools zur Anpassung bündelt | Kostenlos |
| [ZKA 100-Tage Guide](https://zentrum-klimaanpassung.de/wissen-klimaanpassung/beruf-klimaanpassungsmanagerin/die-ersten-100-tage-im-klimaanpassungsmanagement) | Einarbeitungsleitfaden für neue KAMs | Kostenlos, öffentlich verfügbar |

**Was Studierende NICHT tun:** Politische Mandatsverhandlung, Leitung von Stakeholder-Workshops (sie unterstützen und dokumentieren), finale Risikobewertungsentscheidungen, Fördermittelantragstellung (Studierende bereiten vor, Kommune unterzeichnet).

**Unterstützungsinfrastruktur:** Das ZKA bietet kostenlose Schulungsseminare (2,5 Tage, 6x/Jahr), ein Mentoring-Programm (Peer-Matching) und eine digitale Plattform für KAMs. Der Zugang für Werkstudenten zu diesen Programmen ist nicht explizit ausgeschlossen, aber nicht getestet — Anmeldeformulare erlauben flexible Angaben zu Stellenart und -umfang.

#### Stellenbeschreibung

Deutsche Stellenbeschreibung für die Werkstudentenstelle. Inhaltlich abgeleitet von der Agenda — die Stellenbeschreibung ist die Agenda übersetzt in ein Rekrutierungsformat.

**Beschäftigungsmodell:**

| Rolle | Akteur |
|-------|--------|
| **Arbeitgeber** | Kommune (nicht der e.V.) |
| **Vermittler** | Klimafolgenschutz e.V. — findet und verbindet Kandidaten |
| **Methodik** | Die Agenda (Teil 1) leitet die Arbeit der Studierenden |
| **Finanzierung** | **Ungeklärt:** DAS-Förderfähigkeit für Werkstudenten ist nicht getestet |

**Alternative Beschäftigungswege:**

| Weg | Beschreibung | Vorteil |
|-----|-------------|---------|
| **Werkstudent** | Max. 20 Std./Woche im Semester, Anstellung bei Kommune | Standardmodell, flexible Arbeitszeiten |
| **Übungsleiter** | Beschäftigungsmodell über § 3 Nr. 26 EStG (Übungsleiterpauschale) | Steuerfreier Freibetrag bis 3.000 €/Jahr, niedrigere Hürde für Kommunen |
| **Pflichtpraktikum** | Curricularer Bestandteil des Studiengangs | Kein Lohnaufwand für Kommune, Abschlussarbeit-Integration |

**Zielkandidaten:**

| Merkmal | Detail |
|---------|--------|
| **Typ** | Werkstudent (max. 20 Std./Woche im Semester) |
| **Fachrichtungen** | Tiefbauingenieurwesen, Wasserwirtschaft, Geographie, Umweltwissenschaften, BWL/Ökonomie |
| **Einsatzort** | Vor Ort in der Kommune — direkter Einsatz in der Verwaltung |
| **Geltungsbereich** | Bundesweit — keine geografische Einschränkung |
| **Semester** | Ab dem 3. Semester |
| **Abschlussarbeit** | Optional — Studierende können ihre Bachelorarbeit zum Einsatzthema schreiben |

**Stellenbeschreibung Inhalt (Entwurfsstruktur):**

- **Titel:** Werkstudent/in Klimaanpassungsmanagement (m/w/d)
- **Über Klimafolgenschutz e.V.:** Vereinsmission, kommunale Anbindung
- **Deine Aufgaben:** Abgeleitet aus Agenda-Modulen 1–4
- **Dein Profil:** Fachrichtungsanforderungen, Interesse an Klimaanpassung, strukturierte Arbeitsweise, Deutsch fließend, GIS von Vorteil
- **Wir bieten:** Praxiserfahrung, Bachelorarbeit-Möglichkeit, ZKA-Netzwerkzugang, flexible Arbeitszeiten, direkter kommunaler Impact

**Marktpositionierung:** Der KAM-Werkstudent existiert am aktuellen Arbeitsmarkt nicht. Die Differenzierung gegenüber professionellen KAM-Ausschreibungen: niedrigere Einstiegshürde (Student, kein Abschluss erforderlich), Abschlussarbeit-Integration, strukturierte Methodik über die Agenda und ZKA-Netzwerkzugang über die e.V.-Anbindung.

#### LinkedIn Unternehmensseite + Content-Strategie

Neue organisationale LinkedIn-Seite für Klimafolgenschutz e.V. — bisher existiert keine Vereinspräsenz auf LinkedIn. Die Seite bedient gleichzeitig zwei Zielgruppen (Tesla-Modell): Studierende auf der Suche nach Möglichkeiten und Kommunen auf der Suche nach Personallösungen.

**Seiteneinrichtung:**

| Attribut | Wert |
|----------|------|
| **Seitenname** | Klimafolgenschutz e.V. |
| **Kategorie** | Gemeinnützige Organisation |
| **Tagline** | *Gemeinden stärken. Klima anpassen. Zukunft sichern.* |
| **Website** | klimafolgenschutz.com |
| **E-Mail** | info@klimafolgenschutz.org |
| **Admin** | Vereinsvorstand erhält Admin-Zugang |

**Content-Säulen:**

| Säule | Anteil | Zielgruppe | Beispiel |
|-------|--------|------------|---------|
| **Klima-Wissen** | ~30% | Beide | GERICS Klimaausblick-Highlights, Schwammstadt-Erklärer |
| **Karriere** | ~30% | Studierende | Berufsfeld KAM erklären, Werkstudenten-Stellen |
| **Gemeinden** | ~25% | Kommunen | KAnG-Pflichten, DAS-Förder-Updates, Erfolgsgeschichten |
| **Verein** | ~15% | Beide | e.V.-Meilensteine, PIK/UBA-Kontakt-Updates |

**Posting-Frequenz:** 1–2x pro Woche (Regelbetrieb).

**Content-Freigabe:** **Ungeklärt:** Prüft der Vereinsvorstand Beiträge vor der Veröffentlichung?

**Tonalität:** Professionell aber zugänglich — behördennahe Glaubwürdigkeit mit studierendenfreundlicher Sprache. Nur Deutsch (DACH-Fokus).

#### Website-CTA-Integration

Änderungen am bestehenden Klimaanpassungsmanager-Bereich auf klimafolgenschutz.com. Dies ist der einfachste Liefergegenstand — eine Umbenennung, ein Link-Tausch und Badge-Entfernung.

**Änderungen:**

| Element | Aktuell | Neu |
|---------|---------|-----|
| **Abschnittstitel** | Klimafolgenschutzmanager | **Klimaanpassungsmanager** |
| **CTA-Button** | Interesse bekunden → mailto:info@klimafolgenschutz.org | Interesse bekunden → **LinkedIn Stellenanzeige URL** |
| **Badge** | Verfügbarkeitshinweis | **Entfernen** |
| **Subtext** | Aufbauhinweis | **Entfernen** |

**Umbenennungsumfang:** Seitenweit — jede Instanz von Klimafolgenschutzmanager wird zu Klimaanpassungsmanager. Begründung: Klimaanpassungsmanager ist die behördlich etablierte Begrifflichkeit.

**Abhängigkeit:** LinkedIn-Seite + Stellenanzeige muss live sein, bevor der CTA darauf verweisen kann.

```
LinkedIn-Seite live ──→ CTA-URL tauschen
```

#### Screening + Akquise-Kanäle

Die Pipeline erweitert sich um Screening-Schritte und physische Kanäle jenseits der digitalen Präsenz.

**Screening:**

| Schritt | Methode | Automatisierung |
|---------|---------|-----------------|
| **Bewerbungseingang** | Online-Formular | KI-gestütztes Vorscreening |
| **Persönlichkeitstest** | Standardisierter Test (Prinzipien-basiert) | KI-Auswertung, Human-on-the-Loop |
| **Assessment Center** | Optional — für Kandidaten mit hohem Potenzial | Manuell |

**Mindestanforderung:** Ab dem 3. Semester.

**Akquise-Kanäle:**

| Kanal | Typ | Status |
|-------|-----|--------|
| **LinkedIn Stellenanzeige** | Digital | Definiert (Teil 3) |
| **Website CTA** | Digital | Definiert (Teil 4) |
| **Bachelormesse München** | Physisch | 25. April 2026 — Michael nimmt teil |
| **Jobmesse Flughafen München** | Physisch | Juni 2026 |
| **Campus Community Partnerships** | Partnerschaft | "Bildung durch Verantwortung", "Hochschulen engagiert" — Kooperation mit bestehenden Netzwerken |
| **Hochschulmessen** | Physisch | Fokus Umwelt-/Ingenieurswissenschaften |
| **Lead-Generation-Unternehmen** | Extern | Spezialisierte Firmen für Gemeinden-Ansprache |

### Meilenstein 2: Gemeinden-Akquise

Paralleler Aufbau der Nachfrageseite — Gemeinden als Einsatzorte für Werkstudenten gewinnen. Die ersten 100 Gemeinden erhalten eine kostenlose Vereinsmitgliedschaft. Gemeinden registrieren ihren Bedarf; die Plattform verbindet sie mit passenden Werkstudenten.

**Zielgröße:** 200 Gemeinden zum 1. September 2026.

#### Leistungspaket für Gemeinden

Was eine teilnehmende Gemeinde vom Verein erhält:

| Leistung | Beschreibung |
|----------|-------------|
| **Kostenlose Mitgliedschaft** | Die ersten 100 Gemeinden werden kostenlos Vereinsmitglied — kein Risiko, keine Kosten |
| **Dedizierter Klimaanpassungsmanager** | Die Gemeinde erhält einen zugewiesenen Werkstudenten: "In den nächsten Tagen meldet sich Ihr eigener Klimaanpassungsmanager, der für Sie zuständig ist" |
| **Bedarfsprofil** | Die Gemeinde beschreibt ihren spezifischen Bedarf — Geodatenanalyse, Sensorinfrastruktur, Risikokartierung, Wasserwirtschaft — und erhält einen passend qualifizierten Studierenden |
| **Strukturierter Einsatz** | Der Werkstudent arbeitet nach der Klimalotse-Agenda (M1) — keine Eigenentwicklung nötig |
| **Fortschritts-Monitoring** | Ticketsystem für Transparenz: Anrufe, Besuche, Analysen — "sodass man immer aus der Ferne verfolgen kann, wie weit die sind" (Michael) |

**Go-to-Market:** Push-basiertes Modell (Fiverr, nicht Tinder). Die Gemeinde registriert sich und meldet Bedarf. Das System identifiziert passende Werkstudenten und stellt den Kontakt her. Kein aktives Suchen durch die Gemeinde nötig.

**Kommunale Bedarfe (aus dem Brainstorming):**
- Geodatenanalyse (PIK, Deutscher Wetterdienst, Satellitenüberflugdaten)
- Sensorinfrastruktur an neuralgischen Pumpen und Pegelständen
- Geologische Risikokartierung
- Digitalisierung der Wasserwirtschaft und Neuorganisation

#### Sales Channels für Gemeinden

**Ungeklärt:** Über welche Kanäle erreichen wir kommunale Entscheider? → Meeting Agenda

Brainstorming-Ansätze als Ausgangspunkte (nicht darauf beschränkt):
- Lead-Generation-Unternehmen mit Gemeinden-Fokus (Michael)
- Campus Community Partnerships — "Bildung durch Verantwortung", "Hochschulen engagiert" (Ralph)
- Michaels bestehendes BAFA/PEK-Netzwerk
- Mund-zu-Mund zwischen Gemeinden: "Die Gemeinden sprechen sicherlich auch untereinander" (Ralph)
- Webseiten, Reels, Werbeanschreiben (Michael)

#### Sales Funnel für Gemeinden

**Ungeklärt:** Wie verläuft der formalisierte Prozess von Erstkontakt bis Bedarfsmeldung? → Meeting Agenda

Konzeptioneller Ablauf (aus Brainstorming, nicht formalisiert):
```
Problemwahrnehmung (KAnG-Pflicht) → Kontakt mit Verein → Registrierung als Mitglied → Bedarfsprofil ausfüllen → Werkstudent wird zugewiesen → Einsatz beginnt
```

#### Pricing nach den ersten 100

**Ungeklärt:** Was passiert nach der kostenlosen Phase? Mitgliedsbeitrag? Vermittlungsgebühr? Retainer-Modell? → Meeting Agenda

### Meilenstein 3: KI-basiertes Matching + Projektabwicklung (zurückgestellt)

Langfristvision: Ein KI-System verbindet registrierte Werkstudenten mit Gemeinden, die Bedarf gemeldet haben. Setzt M1 (Werkstudenten im System) und M2 (Gemeinden im System) voraus.

**Matching-Modell:** Push-basiert (Fiverr-Modell, nicht Tinder). Gemeinden registrieren Bedarf. Die KI identifiziert passende Werkstudenten und benachrichtigt sie aktiv.

**Matching-Dimensionen:**

| Dimension | Datenquelle |
|-----------|------------|
| **Standort** | Ortsbasiertes Match — Werkstudent in der Nähe der Kommune |
| **Fachrichtung** | Studiengang, Semester, fachliche Schwerpunkte |
| **Persönlichkeit** | Standardisierter Test (aus M1 Screening) |

**Automatisierungsstufen:**
1. Halbmanuell — KI schlägt vor, Mensch entscheidet (Human-on-the-Loop)
2. Halbautomatisch — KI entscheidet, Mensch prüft Ausnahmen
3. Vollautomatisch — KI-Matching mit Alarmglocken bei Sonderfällen

**Aktivierung:** Sobald M1 und M2 eine kritische Masse erreicht haben (~85 Werkstudenten).

---

## Anhang: Institutionen & Begriffsreferenz

Nachschlagewerk zu den im Vorgehen referenzierten Institutionen, rechtlichen Rahmenbedingungen und Fachbegriffen.

### Institutionelle Landschaft

| Institution | Vollständiger Name | Rolle | Relevanz |
|-------------|-------------------|-------|----------|
| **UBA** | Umweltbundesamt | Entwickelt Methodik-Tools (Klimalotse, Anpassungsscanner, Tatenbank) | Quelle des operativen Rahmenwerks, das Studierende nutzen |
| **PIK** | Potsdam-Institut für Klimafolgenforschung | Produziert Klimawissenschaft und -projektionen | Staatlich gefördert, Inhalte sind öffentlich lizenziert — 1:1 wiederverwendbar für die Agenda |
| **GERICS** | Climate Service Center Germany (Helmholtz) | Übersetzt Wissenschaft in kommunenspezifische Klimaberichte (Klimaausblicke) | Ein PDF pro Landkreis mit lokalen Projektionen — die Ausgangsdaten der Studierenden |
| **ZKA** | Zentrum KlimaAnpassung (betrieben von adelphi für BMUKN) | Unterstützt angestellte KAMs: Schulungen (2,5-Tage-Seminare, 6x/Jahr), Mentoring, 100-Tage-Einarbeitungsleitfaden | Kostenlos für geförderte KAMs; Werkstudenten-Berechtigung nicht getestet |
| **ZUG** | Zukunft – Umwelt – Gesellschaft | Verteilt Bundesfördermittel (DAS für Anpassung, NKI für Klimaschutz) | Projektträger — verwaltet die Mittel, nicht die Methodik |
| **KLiVO** | Deutsches Klimavorsorgeportal | Regierungs-Metaportal, das alle Bundestools zur Klimaanpassung indexiert | Suchmaschine, kein Tool — findet Klimalotse, Anpassungsscanner, Tatenbank an einem Ort |

### Rechts- & Förderrahmen

| Begriff | Definition |
|---------|-----------|
| **KAnG** (Klimaanpassungsgesetz) | Bundesgesetz (Nov 2023), das jede Kommune zur Erstellung eines Klimaanpassungskonzepts verpflichtet. Länder müssen bis 31. Jan 2027 Anpassungsstrategien vorlegen. Keine expliziten Sanktionen — gesetzliche Verpflichtung ohne Durchsetzungsmechanismus, erzeugt aber politischen Druck und Haushaltsbegründung. |
| **DAS** (Deutsche Anpassungsstrategie) | Bundesförderprogramm zur Finanzierung von KAM-Stellen und Anpassungskonzepten. Budget seit Aug 2025 erschöpft; Förderrichtlinie gültig bis Dez 2026. Die Regeln bestehen weiterhin, neue Förderfenster sind geplant aber nicht terminiert. |
| **NKI** (Nationale Klimaschutzinitiative) | Parallelprogramm für Klima*schutz* (Mitigation), nicht Anpassung. Finanziert Klimaschutzmanager-Stellen. Gleicher Träger (ZUG), anderer Zweck. |
| **TVöD EG 10–11** | Tarifliche Eingruppierung für professionelle KAMs: ~3.500–4.500 €/Monat brutto. Relevant als Positionierungs-Benchmark — Werkstudenten kosten einen Bruchteil. |
| **Klimaanpassungskonzept** | Das Dokument, das Kommunen gemäß KAnG erstellen müssen: eine formale Klimarisikoanalyse, Anpassungsstrategie und Umsetzungsplan. |
| **Ratsbeschluss** | Gemeinderatsbeschluss — die politische Verabschiedung der Klimaanpassungsstrategie. Erforderlich zur Formalisierung der Strategie. |

### Methodik-Begriffe

| Begriff | Definition |
|---------|-----------|
| **Klimalotse 3.0** | UBA-Schritt-für-Schritt-Anpassungsleitfaden (Website + herunterladbares PDF). Fünf aufeinanderfolgende Module mit Vorlagen und Checklisten. Kein Rechentool — ein strukturierter Workflow, der die Grundlage für ein Klimaanpassungskonzept liefert. |
| **Wirkungsketten** | Kausalketten, die Klimatreiber → Exposition → Empfindlichkeit → Auswirkung abbilden. Beispiel: *Steigende Temperaturen → Hitzewellen → Städtische Oberflächen strahlen Wärme ab → Erhöhte Hitzesterblichkeit.* Werden in Modul 2 verwendet, um systematisch betroffene Sektoren zu identifizieren. |
| **Anpassungsscanner** | UBA-Excel-Selbstbewertung zur Messung der institutionellen Bereitschaft (nicht des physischen Risikos). Fünf Dimensionen: (1) Werte & Ziele, (2) Wissenszugang, (3) Zusammenarbeit, (4) Ressourcen, (5) Strategien. Ergebnis: Score von 0–100. |
| **GERICS Klimaausblicke** | Automatisch generierte PDFs mit Klimaprojektionen pro Landkreis über drei RCP-Szenarien. Indikatoren umfassen: Jahresmitteltemperatur, Heiße Tage (≥30°C), Tropennächte, Frosttage, Starkniederschlag, Trockenperioden, Schneebedeckung. |
| **RCP-Szenarien** | Standardisierte Emissionspfade: RCP 2.6 (starke Mitigation, ~+1,5–2°C), RCP 4.5 (moderat, ~+2–3°C), RCP 8.5 (Business as usual, ~+3,5–5°C). Verwendet in allen GERICS-Projektionen. |
| **Tatenbank** | UBA-Datenbank mit 432 realen Anpassungsprojekten. Good-Practice-Bibliothek, durchsuchbar nach Handlungsfeld, Klimaauswirkung und Region. Studierende nutzen sie in Modul 3 zur Identifikation bewährter Maßnahmen. |
| **DIN EN ISO 14091** | Internationaler Standard für Klimarisikoanalyse (2021). Best Practice, nicht verpflichtend — wird aber zunehmend in formalen Konzepten und Ausschreibungen referenziert. |
| **Schwammstadt** | Stadtplanungskonzept: Regenwasser aufnehmen, speichern und verzögert abgeben statt in die Kanalisation zu leiten. Konkrete Elemente: wasserdurchlässige Beläge, Gründächer, Mulden-Rigolen, Stadtbäume, Entsiegelung. |
| **Maßnahmensteckbriefe** | Standardisierte Maßnahmenprofile zur Dokumentation einzelner Anpassungsmaßnahmen — Was, Wo, Kosten, Zeitplan. Studierende erstellen diese in Modul 3. |
| **Fördermittelantragstellung** | Fördermittel-Antragsprozess. Studierende recherchieren Programme und bereiten Anträge vor; die Kommune unterzeichnet und reicht ein. |

### Professionelle Landschaft

| Begriff | Definition |
|---------|-----------|
| **Klimaschutzmanager** (~2.500 bundesweit) | Kommunale Rolle mit Fokus auf Klima*schutz* — CO₂-Emissionen reduzieren (Solar, Sanierung, Mobilität). Gefördert durch NKI. |
| **Klimaanpassungsmanager** (~300 bundesweit) | Kommunale Rolle mit Fokus auf Klima*anpassung* — Vorbereitung auf bereits eintretende Auswirkungen (Hitze, Überflutung, Dürre). Gefördert durch DAS. Die neuere, unterversorgte Rolle. |
| **KAM-Werkstudent** | Die Stellenkategorie, die diese Pipeline schafft. Existiert am aktuellen Markt nicht — niedrigere Einstiegshürde als professioneller KAM (Student, kein Abschluss erforderlich), mit Abschlussarbeit-Integration und strukturierter Methodik über die Agenda. |

---

## Quellen

- [DAS-Förderrichtlinie 2021 (PDF)](https://www.z-u-g.org/fileadmin/zug/Dateien/Foerderprorgamme/DAS_Anpassung_an_den_Klimawande/25-01-DAS-Foerderrichtlinie_2021_ab_06.11.24_bf.pdf) — Bundesförderregeln für KAM
- [KAnG (Bundestag)](https://www.bundestag.de/dokumente/textarchiv/2023/kw46-de-bundesklimaanpassungsgesetz-976584) — Kommunale gesetzliche Verpflichtung
- [UBA Klimalotse 3.0](https://www.umweltbundesamt.de/themen/klimalotse-30-aktualisierter-leitfaden-zur) — 5-Modul-Methodik
- [GERICS Klimaausblicke](https://www.gerics.de/products_and_publications/fact_sheets/landkreise/index.php.de) — 17 Indikatoren pro Landkreis
- [UBA Anpassungsscanner](https://www.umweltbundesamt.de/node/118272) — Excel-Selbstbewertung
- [KLiVO Portal](https://www.klivoportal.de) — Regierungs-Tool-Aggregator
- [ZKA KAM Beruf](https://zentrum-klimaanpassung.de/wissen/beruf-klimaanpassungsmanagerin/beruf-klimaanpassungsmanagerin) — Rollendefinition
- [NKI / DAS Förderung](https://www.z-u-g.org/das/) — Bundesprogramm-Übersicht
- [NKI Kommunalagentur](https://www.klimaschutz.de/de/foerderung-der-nki/ueber-die-nki-dashboard/agentur-fuer-kommunalen-klimaschutz) — Beratung für Kommunen

### Transkripte
- [Grooming 2026-04-03](https://app.fireflies.ai/view/01KN9MYRJHAEWB76FFPHASWZRT) — Marius-Feedback: Undefined-Marker, Sub-Issues closen, Projekttransparenz
- [Brainstorming 2026-04-02](https://app.fireflies.ai/view/01KN6XT46VCJV55FCJGMFPQNFD) — 3-Meilenstein-Struktur, Gemeinden-Leistungspaket, Matching-Vision, Sales-Diskussion
- [Client Sync 2026-03-30](https://app.fireflies.ai/view/01KMZ8E02X2RGB29M3DSFQJVP1) — Team, Beschäftigungsmodelle, Content-Workflow
- [Design Doc Erstellung 2026-03-23](https://app.fireflies.ai/view/01KMDCMMAF48SP1FBQ0JWW51R7) — PIK/UBA-Recherche, Klimalotse-Framework

### Sessions
- Pass 5: `75381981` (2026-04-03) — Projekttransparenz aus Design Doc entfernt (client-facing), als Meeting-Agenda-Topic verschoben
- Pass 4: `04e8096c` (2026-04-03) — Undefined-Marker, Gemeinden-Leistungspaket, Beschäftigungsmodelle, Projekttransparenz, #1368/#1366 gemerged
- Pass 3: `211766c6` (2026-04-02) — Meilenstein-Restrukturierung, M2 + M3 hinzugefügt, Team-Sektion, Screening

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

