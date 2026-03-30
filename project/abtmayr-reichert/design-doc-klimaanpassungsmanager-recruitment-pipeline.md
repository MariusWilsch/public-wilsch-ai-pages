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

**Voraussetzungen:**
- KAnG in Kraft — Kommunen sind gesetzlich zum Handeln verpflichtet
- DAS-Förderfenster geschlossen (Aug 2025), Förderrichtlinie gültig bis Dez 2026
- PIK/UBA stellen kostenlose Methodik-Tools bereit (Klimalotse 3.0, GERICS, Anpassungsscanner)
- Klimafolgenschutz e.V. ist voll operativ — Website live, Bankkonto, Vereinsregister, Steuernummer erhalten, Spendenquittungs-Berechtigung erteilt (Stand März 2026)
- Stellenkategorie „KAM-Werkstudent" existiert am aktuellen Markt nicht

**Organisation:**

| Rolle | Person | Verantwortung |
|-------|--------|---------------|
| **Ambassador** | Michael Reichert | Vision, PIK/UBA-Netzwerk, politische Kontakte, Kommunen-Beziehungen |
| **Geschäftsführer (designiert)** | Ralph Loser | Operatives Management, technische/organisatorische Umsetzung. Softwareentwickler, selbstständig, Kontakt über VCon-Netzwerk |
| **Technischer Partner** | Marius Wilsch / Wilsch AI Services | Design, Entwicklung, KI-Automatisierung, Content-Produktion |

Der e.V. operiert als schlankes Team mit KI-gestützten Workflows. Enge Zusammenarbeit zwischen Ralph und Marius in der Umsetzung.

---

## Erfolgsdefinition

- **Ziel:** Klimafolgenschutz e.V. kann Klimaanpassungsmanager (Werkstudenten) für Kommunen über eine einheitliche Pipeline rekrutieren — von der Stellenbeschreibung über die LinkedIn-Präsenz bis zur Website-Integration — und gleichzeitig Kommunen als Einsatzpartner gewinnen
- **Gemeinsames Verständnis:** Michael, Ralph und Marius teilen dasselbe Verständnis von Scope, Liefergegenständen und Verantwortlichkeiten. Dieses Dokument dient als gemeinsame Referenz für Brainstorming und laufende Umsetzung
- **Umsetzungsreife:** Das Dokument ist detailliert genug, um daraus konkrete Arbeitsaufträge abzuleiten, ohne dass eine weitere Design-Sitzung nötig ist

---

## Vorgehen

Die Pipeline besteht aus zwei parallelen Spuren — Studierenden-Rekrutierung und Kommunen-Akquise — verbunden über die Matching-Plattform. Fünf Teile:

```
STUDIERENDEN-SPUR:  Agenda ──→ Stellenbeschreibung ──→ LinkedIn ──→ Website-CTA
                                                                        ↕
KOMMUNEN-SPUR:      Kommunen-Akquise ──────────────────────→ Website-CTA (Gemeinde)
```

### Teil 1: Klimaanpassungsmanager Agenda

Ein internes Onboarding-Dokument, das Kandidaten nach der Annahme ausgehändigt wird. Es zeigt ihnen, was sie im Einsatz tun werden, und gibt Kommunen Vertrauen in die Methodik. Abgeleitet vom bundesweiten Klimalotse 3.0 Rahmenwerk (UBA), nicht von Grund auf neu erfunden.

**Auslieferung:** Kandidat bewirbt sich → wird angenommen → erhält die Agenda als Arbeitshandbuch. Die Agenda ist kein Bestandteil der Stellenanzeige — sie ist das operative Handbuch für eingesetzte KAMs.

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

**Produktpaket-Vision:** Die am häufigsten geförderten Bundesmaßnahmen sollen identifiziert und als konkrete „Produkte" für Kommunen verpackt werden — z.B. Rückhaltebecken-Vergrößerung mit Umfang, benötigten Gewerken, Architektenleistungen und Förderquote nach Bundesland. Beim Berliner Kontakt (PIK) wurden die Top-3 der meistgeförderten Klimafolgenbearbeitungs-Maßnahmen bundesweit angefragt. **Daten ausstehend** — Michael übernimmt diese Recherche. Nach Erhalt werden diese zu den Ankerprodukten, die sowohl die Agenda als auch das Kommunen-Angebot strukturieren.

**PIK/UBA als Wissensdatenbank:** Das Klimalotse-Rahmenwerk strukturiert die Arbeit der Studierenden. Die PIK/UBA-Forschung dient als Hintergrundwissen — für Argumentation und Fördermittel-Begründung, nicht als Curriculum der Studierenden. Ziel ist praxisorientierte Umsetzung, nicht wissenschaftliche Problematisierung.

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

### Teil 2: Stellenbeschreibung

Deutsche Stellenbeschreibung für die Werkstudentenstelle. Inhaltlich abgeleitet von der Agenda — die Stellenbeschreibung ist die Agenda übersetzt in ein Rekrutierungsformat.

**Beschäftigungsmodell:**

| Rolle | Akteur |
|-------|--------|
| **Arbeitgeber** | Kommune (nicht der e.V.) |
| **Vermittler** | Klimafolgenschutz e.V. — findet und verbindet Kandidaten |
| **Methodik** | Die Agenda (Teil 1) leitet die Arbeit der Studierenden |
| **Finanzierung** | **Ausstehend:** DAS-Förderfähigkeit für Werkstudenten/Übungsleiter ist nicht getestet. Es wird recherchiert, ob die Finanzierung über Kommunen läuft oder direkt vom e.V. beantragt werden kann. |

**Beschäftigungsmodelle (zwei Optionen):**

| Modell | Stunden | Vergütung | Einsatzfall |
|--------|---------|-----------|-------------|
| **Werkstudent** | Max. 20 Std./Woche im Semester | Standard-Werkstudentenvergütung | Studierende mit fortlaufendem Engagement neben dem Studium |
| **Übungsleiter / Minijob** | ~10–12 Std./Woche | 400–500 €/Monat (Minijob-Basis) | Leichterer Einstieg, niedrigere Einstiegsschwelle |

Das Modell kann von Fall zu Fall variieren.

**Zielkandidaten:**

| Merkmal | Detail |
|---------|--------|
| **Typ** | Werkstudent oder Übungsleiter (siehe Beschäftigungsmodelle oben) |
| **Fachrichtungen** | Tiefbauingenieurwesen, Wasserwirtschaft, Geographie, Umweltwissenschaften, BWL/Ökonomie, Bauingenieurwesen, Architektur |
| **Einstieg** | Ab ca. 2. Semester — früher Einstieg mit Blick auf die Bachelorarbeit |
| **Einsatzort** | Vor Ort in der Kommune (im Umkreis von 50–100 km) — direkter Einsatz in der Verwaltung |
| **Geltungsbereich** | Bundesweit — keine geografische Einschränkung |
| **Einsatzdauer** | Bis zu 3 Jahre pro Kommunen-Einsatz |
| **Abschlussarbeit** | Optional — Studierende können ihre Bachelorarbeit zum Einsatzthema schreiben |
| **Praktikum** | Studierende mit Pflichtpraktikum können dieses über den Einsatz erfüllen — ein natürlicher Rekrutierungskanal neben Abschlussarbeit und bezahlter Arbeit |

**Stellenbeschreibung Inhalt (Entwurfsstruktur):**

- **Titel:** Werkstudent/in Klimaanpassungsmanagement (m/w/d)
- **Über Klimafolgenschutz e.V.:** Vereinsmission, kommunale Anbindung
- **Deine Aufgaben:** Abgeleitet aus Agenda-Modulen 1–4
- **Dein Profil:** Fachrichtungsanforderungen, Interesse an Klimaanpassung, strukturierte Arbeitsweise, Deutsch fließend, GIS von Vorteil
- **Wir bieten:** Praxiserfahrung, Bachelorarbeit-Möglichkeit, ZKA-Netzwerkzugang, flexible Arbeitszeiten, direkter kommunaler Impact

**Marktpositionierung:** Der KAM-Werkstudent existiert am aktuellen Arbeitsmarkt nicht. Die Differenzierung gegenüber professionellen KAM-Ausschreibungen: niedrigere Einstiegshürde (Student, kein Abschluss erforderlich), Abschlussarbeit-Integration, strukturierte Methodik über die Agenda und ZKA-Netzwerkzugang über die e.V.-Anbindung.

### Teil 3: LinkedIn Unternehmensseite + Content-Strategie

Neue organisationale LinkedIn-Seite für Klimafolgenschutz e.V. — bisher existiert keine Vereinspräsenz auf LinkedIn. Die Seite bedient gleichzeitig zwei Zielgruppen (Tesla-Modell): Studierende auf der Suche nach Möglichkeiten und Kommunen auf der Suche nach Personallösungen.

**Seiteneinrichtung:**

| Attribut | Wert |
|----------|------|
| **Seitenname** | Klimafolgenschutz e.V. |
| **Kategorie** | Gemeinnützige Organisation |
| **Tagline** | *Gemeinden stärken. Klima anpassen. Zukunft sichern.* |
| **Website** | klimafolgenschutz.com |
| **E-Mail** | info@klimafolgenschutz.org |
| **Admin** | Michael + Ralph erhalten Admin-Zugang, Marius erstellt die Seite |

**Content-Säulen:**

| Säule | Anteil | Zielgruppe | Beispiel |
|-------|--------|------------|---------|
| **Klima-Wissen** | ~30% | Beide | GERICS Klimaausblick-Highlights, Schwammstadt-Erklärer |
| **Karriere** | ~30% | Studierende | Berufsfeld KAM erklären, Werkstudenten-Stellen |
| **Gemeinden** | ~25% | Kommunen | KAnG-Pflichten, DAS-Förder-Updates, Erfolgsgeschichten |
| **Verein** | ~15% | Beide | e.V.-Meilensteine, PIK/UBA-Kontakt-Updates |

**Posting-Frequenz:** 1–2x pro Woche (Regelbetrieb).

**Content-Produktion:** Marius/Team produziert Content. Ralph erhält alle Dokumente vollständig (Einarbeitungsphase) und wird zunehmend in die Content-Zusammenarbeit eingebunden.

**Content-Freigabe:** Das Team veröffentlicht eigenständig. Kein formaler Freigabe-Prozess vor Veröffentlichung erforderlich. Mit Ralphs wachsendem Domänenwissen kann sich eine regelmäßige Content-Abstimmung natürlich entwickeln.

**Tonalität:** Professionell aber zugänglich — behördennahe Glaubwürdigkeit mit studierendenfreundlicher Sprache. Nur Deutsch (DACH-Fokus).

### Teil 4: Website-CTA-Integration

Änderungen am bestehenden Klimaanpassungsmanager-Bereich auf klimafolgenschutz.com. Dies ist der einfachste Liefergegenstand — eine Umbenennung, ein Link-Tausch und Badge-Entfernung.

**Änderungen:**

| Element | Aktuell | Neu |
|---------|---------|-----|
| **Abschnittstitel** | Klimafolgenschutzmanager | **Klimaanpassungsmanager** |
| **CTA-Button** | Interesse bekunden → mailto:info@klimafolgenschutz.org | Interesse bekunden → **LinkedIn Stellenanzeige URL** |
| **Badge** | Verfügbarkeitshinweis | **Entfernen** |
| **Subtext** | Aufbauhinweis | **Entfernen** |

**Umbenennungsumfang:** Seitenweit — jede Instanz von Klimafolgenschutzmanager wird zu Klimaanpassungsmanager. Begründung: Klimaanpassungsmanager ist die behördlich etablierte Begrifflichkeit.

**Kommunen-Pfad:** Die Website benötigt zusätzlich einen kommunenseitigen CTA — Kommunen sollen ihr Interesse an einem KAM signalisieren können. Das genaue Format und der Ablauf hängen von Teil 5 (Kommunen-Akquise) ab. Mindestens: ein Bereich oder eine Seite, auf der Kommunen ihr Interesse registrieren können.

**Abhängigkeiten:**

```
Teil 3 (LinkedIn-Seite live) ──→ Teil 4 (Studierenden-CTA-URL tauschen)
Teil 5 (Kommunen-Akquise definiert) ──→ Teil 4 (Kommunen-CTA hinzufügen)
```

**Skalierungsvision:** Das Langzeitziel ist eine vollautomatisierte Matching-Plattform — Studierende bewerben sich, KI prüft Qualifikationen, nahegelegene Kommunen werden zugeordnet, Zuweisungen erfolgen ohne manuellen Aufwand. Dies ist das langfristige Architekturziel, nicht der unmittelbare Scope — aber jeder Teil sollte mit Automatisierungsbereitschaft konzipiert werden.

### Teil 5: Kommunen-Akquise (Parallele Spur)

Kernprinzip: Angebot und Nachfrage gleichzeitig aufbauen. Während die Teile 1–4 die Studierenden-Pipeline schaffen, baut Teil 5 die Kommunen-Pipeline auf. Keines soll auf das andere warten.

**Modell:** Der e.V. operiert als Matching-Plattform — Studierende auf der einen Seite, Kommunen auf der anderen. Der e.V. rekrutiert beide und verbindet sie basierend auf Geografie (50–100 km Umkreis) und Themenschwerpunkt.

**Wertversprechen für Kommunen:** Die Kommune erhält einen KAM ohne vollständiges TVöD-Einstellungsverfahren. Der Studierende kommt mit strukturierter Methodik (die Agenda), unterstützt durch PIK/UBA-Tools, und der e.V. übernimmt Rekrutierung und Einarbeitung. Für Kommunen, die noch nicht mit ihrem Klimaanpassungskonzept begonnen haben (wozu das KAnG jetzt verpflichtet), ist dies ein niedrigschwelliger Einstiegspunkt.

**Bekannte Anforderungen:**
- Kommunen müssen ihr Interesse registrieren können (Website oder Direktansprache)
- Geografisches Matching: Studierende werden innerhalb pendelbarer Entfernung zugewiesen
- Gemeinderatsbeschluss kann erforderlich sein, bevor eine Kommune einen KAM aufnehmen kann — dies verlängert die Vorlaufzeit
- Die Produktpakete aus Teil 1 (meistgeförderte Maßnahmen) werden zum Kommunen-Angebot

**Was definiert ist:** Das Konzept, das Wertversprechen, das Matching-Modell.
**Was nicht definiert ist:** Kommunen-Ansprachekanal, Onboarding-Prozess für Kommunen, Vertragsstruktur zwischen e.V. und Kommune, Zeitplan. → Wird im Donnerstags-Brainstorming (2026-04-03) erarbeitet.

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

- **Transkripte:**
  - [Kundengespräch 2026-03-23](https://app.fireflies.ai/view/01KMDCMMAF48SP1FBQ0JWW51R7) — Primäre Anforderungserhebung (Michael, Marius, David)
  - [Kundengespräch 2026-03-30](https://app.fireflies.ai/view/01KMZ8E02X2RGB29M3DSFQJVP1) — Sync mit Ralph Loser (neuer Geschäftsführer), Beschäftigungsmodell-Erweiterung, parallele Kommunen-Akquise, Projektplan-Diskussion
- [DAS-Förderrichtlinie 2021 (PDF)](https://www.z-u-g.org/fileadmin/zug/Dateien/Foerderprorgamme/DAS_Anpassung_an_den_Klimawande/25-01-DAS-Foerderrichtlinie_2021_ab_06.11.24_bf.pdf) — Bundesförderregeln für KAM
- [KAnG (Bundestag)](https://www.bundestag.de/dokumente/textarchiv/2023/kw46-de-bundesklimaanpassungsgesetz-976584) — Kommunale gesetzliche Verpflichtung
- [UBA Klimalotse 3.0](https://www.umweltbundesamt.de/themen/klimalotse-30-aktualisierter-leitfaden-zur) — 5-Modul-Methodik
- [GERICS Klimaausblicke](https://www.gerics.de/products_and_publications/fact_sheets/landkreise/index.php.de) — 17 Indikatoren pro Landkreis
- [UBA Anpassungsscanner](https://www.umweltbundesamt.de/node/118272) — Excel-Selbstbewertung
- [KLiVO Portal](https://www.klivoportal.de) — Regierungs-Tool-Aggregator
- [ZKA KAM Beruf](https://zentrum-klimaanpassung.de/wissen/beruf-klimaanpassungsmanagerin/beruf-klimaanpassungsmanagerin) — Rollendefinition
- [NKI / DAS Förderung](https://www.z-u-g.org/das/) — Bundesprogramm-Übersicht
- [NKI Kommunalagentur](https://www.klimaschutz.de/de/foerderung-der-nki/ueber-die-nki-dashboard/agentur-fuer-kommunalen-klimaschutz) — Beratung für Kommunen

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

