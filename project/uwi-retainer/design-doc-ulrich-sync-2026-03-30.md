---
publish: true
---

# Design Doc: Ulrich Sync — 30. März 2026
[[client-wilsch-group]]

Dokumentiert Entscheidungen, zurückgestellte Themen und Action Items aus den Ulrich-Sync-Meetings (30. März + 2. April 2026). Ursprünglich 9 Themen — erweitert auf 11 nach dem April-Follow-up mit Ostermann und Angebotsautomatisierung.

---

## Problem Statement

Elf operative Themen über den UWI / Wilsch Group Retainer — Vertragsverlängerung, offene Rechnungen, Seminar-Follow-up, Datensicherheit, NDA, IBM Power Bewertung, Produktverpackung, Newsletter-Strategie, internationale Expansion, Ostermann-Dokumentation und Angebotsautomatisierung. Jedes Thema erforderte Stakeholder-Input von Ulrich und Thomas, der nicht asynchron lösbar war.

**Vorbedingungen:**
- Aktueller Supportvertrag monatlich verlängert (€1.250/Monat) seit 30. März 2026 — FDI-Nachfolgemodell in Designphase
- FSO-Seminar am 27. März abgeschlossen mit 4 interessierten Kontakten für Follow-up
- DGX Spark Hardware erworben, aber Eigentums-/Abrechnungsstruktur ungeklärt
- Mehrere aufgeschobene Themen aus früheren Syncs (NDA, Datensicherheit, Newsletter)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Ziel** | Alle Stakeholder abgestimmt auf Entscheidungen, Verantwortlichkeiten und Zeitpläne für alle 11 Agenda-Themen + Bonus-Punkte (über beide Syncs hinweg: 30. März + 2. April) |
| **Erfolg** | Jedes Thema hat ein klares Ergebnis — gelöst, aufgeschoben mit nächstem Schritt, oder begraben — lesbar für jemanden, der nicht im Raum war |
| **Done-Test** | Können Ulrich, Thomas, Marius und David ihre Action Items und Deadlines allein aus diesem Dokument identifizieren? |

---

## Approach

Elf Agenda-Themen (9 Original + 2 aus dem 2. April Follow-up), in Diskussionsreihenfolge. Jedes beantwortet seine "Zu klären"-Fragen mit einem Ergebnis: ✅ gelöst, ⏳ aufgeschoben, 🪦 begraben, ❌ nicht besprochen.

### Part 1: Vertrag — Rahmenvertrag ab April ⏳

Der aktuelle Retainer wurde um einen Monat zu €1.250 verlängert. Kein Nachfolge-Vertragsmodell vereinbart.

Rollen geklärt: Ulrich bekommt 10% Verkaufsprovision ohne Projektverantwortung oder Haftung. Thomas dient als Rechnungsempfänger (Fakturadresse). Marius und Thomas teilen die verbleibenden 90% pro Projekt — genaue Aufteilung noch offen.

Marius entwickelt ein "Design Partner"-Modell (Retainer + Success Fee, inspiriert von Palantirs FDI-Ansatz) innerhalb von zwei Wochen.

**2. April Update:** Das FDI-Modell wurde ausführlich besprochen. Marius erklärte das Forward Deployed Engineer Konzept — einen dedizierten KI-Spezialisten bei einer Kundenfirma einbetten, 80%-fertige KI-Bausteine nutzen, kontinuierlich anpassen. Ulrichs Position unverändert: 10% Pauschalprovision, gleiches Modell wie der bestehende 10-jährige Ulrich-Thomas-Vertrag. April läuft auf dem bestehenden Monatsmodell weiter, während Marius das FDI-Preisdokument designt.

| Action | Owner | Timeline |
|--------|-------|----------|
| FDI-Preismodell + Positionsdokument designen | Marius | 2 Wochen (ab 2. April) |
| Bestehenden Ulrich-Thomas-Vertrag an Marius senden | Ulrich | — |
| Produktlinien-Diskussion | Marius + Ulrich | Samstag (Ostern) |

**Undefined:** Vertragsumfang und Startdatum → aufgeschoben bis FDI-Preismodell fertig
**Undefined:** Interne 90%-Aufteilung (Marius/Thomas pro Projekt) → aufgeschoben bis FDI-Preismodell fertig

### Part 2: Rechnungen — Rekers + DGX Spark ✅

**Rekers:** Marius bereitet die Rechnung zu 40% an WPH vor, vorbehaltlich Thomas' Bestätigung des Splits (heute/morgen). Ulrich ruft Witak direkt an, um ein kleines Entscheider-Team statt voller Gruppenmeetings zu etablieren.

**DGX Spark:** Thomas besitzt die Hardware zu 100%. Marius stellt Thomas die Weiterberechnung (über seine estnische Firma). Das Refinanzierungsmodell ist €500/Monat in der Projektabrechnung, bereits aktiv mit AWO. Die DGX-Einheit zieht im April in Thomas' Rechenzentrum um; VPN- und SSH-Zugang für Marius folgt.

**2. April Update:** Rekers 40%-Split bestätigt. Thomas sendet die Rekers-Rechnung heute, Kopie an Marius. Marius sendet die DGX-Weiterberechnungs-Rechnung parallel. DGX-Umzug ins Rechenzentrum planmäßig im April.

| Action | Owner | Timeline |
|--------|-------|----------|
| ~~Rekers 40%-Split bestätigen~~ | Thomas | ✅ Bestätigt (40%) |
| Rekers-Rechnung senden | Thomas | 2. April |
| DGX-Rechnung senden | Marius | 2. April |
| Witak anrufen (Entscheider-Team) | Ulrich | Offen |
| Weiterberechnung DGX an Thomas | Marius | — |
| DGX in Rechenzentrum umziehen | Thomas | April |

### Part 3: FSO-Seminar — Follow-up ✅

Das Seminar ergab 3–4 interessierte Kontakte: Pöckemöller, Hasloff, Arndt und einen weiteren (Name nicht erinnert). Ulrich telefoniert nächste Woche alle nach, Ziel: DGX Spark Bestellungen.

Separat: Ulrich vergibt 10 Test-Website-Zugänge an Seminarteilnehmer und trackt Nutzung und Zahlungsbereitschaft. Thomas und David sind skeptisch bezüglich bezahlter Inhalte.

**2. April Update:** Seminarkontakte zu aktiven Leads konvertiert. Hasloff ist der heißeste — sein Geschäftsführer will das Team treffen. Poggemüller und Amari ebenfalls aktiv, alle wollen DB/ERP-Datenzugang (gleiches Use-Case-Muster). Die 10 Test-Zugänge haben sich zum [#1334 PDF-Angebotsgenerator-System](https://github.com/DaveX2001/deliverable-tracking/issues/1334) entwickelt — individuelle 30-Tage E-Mail-basierte Interessenten-Konten mit Nutzungstracking und On-Demand PDF-Angebotserstellung.

| Action | Owner | Timeline |
|--------|-------|----------|
| ~~Seminar-Kontakte nachtelefonieren~~ | Ulrich | ✅ Leads aktiv |
| 30-Tage Interessenten-Konten + PDF-Angebotsgenerator (#1334) | David | Wartet auf Ulrichs Referenz-Dokumente |

**Undefined:** Nächstes Seminar-Datum — in keinem der beiden Meetings besprochen.

### Part 4: Kundendaten auf GitHub ✅

Architekturansatz am 2. April entschieden: Eine separate Linux-Maschine läuft neben dem DGX Spark im Rechenzentrum und hostet eine self-hosted GitHub/GitLab-Instanz. Die beiden Maschinen kommunizieren intern und bilden eine DMZ — eine isolierte Zone für KI-Systeme, in der Kundendaten nicht im öffentlichen Internet liegen. Nicht auf dem DGX selbst (Verschwendung für einen Git-Server).

Ulrichs Kernanforderung: *"Mir geht es nur darum die Daten sind dann nicht im Internet."*

| Action | Owner | Timeline |
|--------|-------|----------|
| DMZ-Architektur + GitHub-Hosting besprechen | Marius + Thomas | Dienstag nach Ostern, ab 10 Uhr |

### Part 5: NDA für David ✅

Am 2. April gelöst. Die NDA ist zwischen Marius und David — nicht WPH und David. Thomas stellte klar: WPH braucht nur eine NDA mit David, wenn David ein direktes Vertragsverhältnis mit WPH hätte. Der bestehende NDA-Entwurf (am 1. März von Marius gesendet) wurde nie geprüft, weil weder Ulrich noch Chris Lemke geantwortet haben.

Weiteres Vorgehen: Thomas weist Chris (Lemke) im ISO-Meeting an, auf Marius' NDA-Entwurf zu antworten. Marius wartet auf Chris' Antwort.

| Action | Owner | Timeline |
|--------|-------|----------|
| Chris anweisen, auf NDA zu antworten | Thomas | 2. April (ISO-Meeting) |
| Auf Chris' Antwort warten | Marius | — |

### Part 6: IBM Power / Kernel-Entwickler 🪦

Einstimmig begraben. Thomas identifizierte einen fundamentalen Architektur-Mismatch — der Chip wurde nicht für KI-Workloads designt. Keine Aktivität für 12–24 Monate geplant. Mögliche Neubewertung wenn Power 12 mit GPU-freundlichem Design erscheint.

### Part 7: KI aus der Box + Rekers-Preis ✅

**KI aus der Box:** Bestellungen laufen über das bestehende Warenwirtschaftssystem der Wilsch KG — Ulrich legt 4 Artikel an, Busch generiert Auftragsbestätigungen. Kunden wollen ein formelles Angebot vor Bestellung, kein direkter Stripe-Checkout. David baut einen PDF-Angebotsgenerator (Markdown → PDF). Zwei Produktlinien zur Vereinfachung: (1) Expertise-Buchung, (2) DGX Spark Hardware + Setup.

→ [Issue #1334](https://github.com/DaveX2001/deliverable-tracking/issues/1334) — PDF-Angebotsgenerator

**2. April Update:** Ulrich demonstrierte die bestehende "Angebotsübersicht"-Vorlage per Bildschirmfreigabe — ein zweidimensionaler Systemvergleich (alt vs. neu) mit Hardware-Specs, Software-Komponenten, Dienstleistungen und Preisen. Dies wird die PDF-Vorlage für #1334. Anforderungen verfeinert:
- Individueller E-Mail-basierter Login pro Interessent (keine geteilten Accounts)
- 30-Tage automatischer Ablauf pro Account
- Nutzungstracking: welche Seiten, wie lange, pro Account (Heatmap-Stil)
- PDF-Ausgabe: max. 2 Seiten OnePager — *"Verliert euch nicht in zu vielen technischen Details... so ein One Pager vor der Rückseite kommt gut an."*
- Eingabe: Kundenname + Firma + E-Mail → automatisch in gebrandetes PDF übernommen

Ulrich sendet die "Systemvergleich"- und "Angebotsübersicht"-Referenzdokumente an David.

**Rekers-Preisschätzung:** Keine Preisarbeit bis die Rekers-Rechnung (Part 2) gesendet und bezahlt ist. Das Step-2-Angebot wird aus AWOs bestehendem Step-2-Angebot als Basis abgeleitet. Nächstes Rekers-Meeting in 2–3 Wochen nach Ostern erwartet.

| Action | Owner | Timeline |
|--------|-------|----------|
| PDF-Angebotsgenerator erstellen (#1334) | David | Wartet auf Ulrichs Referenz-Dokumente |
| Systemvergleich + Angebotsübersicht PDFs senden | Ulrich | — |
| 4 Artikel in Warenwirtschaft anlegen | Ulrich/Busch | — |

### Part 8: Newsletter ⏳

3–6 Monate als eigenständige Initiative aufgeschoben. Marius verantwortet es als Teil seiner persönlichen Marketing-Struktur — aktuell nicht relevant für Ulrich/Thomas. Verbindung zu [#847 Contract Strategy & Retainer Model Design](https://github.com/DaveX2001/deliverable-tracking/issues/847), da Marius die Marketing-Positionierung parallel zum FDI-Preismodell mitdenkt.

Die 10 Test-Website-Zugänge (Part 3) haben sich zum #1334 Interessenten-Account-System entwickelt, das als leichtgewichtiger Content-Distributions-Pilot dient.

### Part 9: Malaysia-Expansion — Kate ⏳

Kates Stundensatz von €190/h wurde als zu hoch für die aktuelle Kontaktvermittler-Rolle bewertet. Ulrich kontaktiert Kate direkt bis Freitag oder Samstag zur Besprechung der Konditionen.

**2. April Update:** Zwei Blocker bestätigt — Preis und Zeit. Ulrich: *"Mein Problem ist auch neben dem Geld die Zeit."* Er wird Kate direkt aber freundlich informieren. Marius plädierte für mindestens einen Verhandlungsversuch — Kate hat Kapazitäten, hilft bei ESG-Positionierung und täglicher Dokumentation, und ist mehr als eine Kontaktvermittlerin. Ulrich hat sich zu einem persönlichen Gespräch mit Kate über Ostern / in Malaysia verpflichtet.

| Action | Owner | Timeline |
|--------|-------|----------|
| Persönliches Gespräch mit Kate | Ulrich | Über Ostern / Malaysia |

**Undefined:** Ob die Zusammenarbeit überhaupt weitergeht — hängt von Ulrichs Gespräch mit Kate ab.

### Part 10: Ostermann — Dokumentation + Testumgebung 🪦/⏳

*Hinzugefügt am 2. April.*

**Testumgebung (#1059):** 🪦 Begraben. Die Ostermann-Maschine als zweite Testumgebung ist praktisch tot — Power 11 würde nichts wesentlich verbessern. Ulrich: *"Ich denke mal das sollte man erstmal beerdigen, außer wir finden irgendwas Neues raus."* Offen seit November.

**Dokumentation (#866):** ⏳ Aufgeschoben auf Ulrich. Er muss die Roh-Dokumentation vorbereiten (Passwörter raus), dann wertet David sie per KI auf — gleicher Ansatz wie beim Design Doc. Ulrich hat zugesagt, es über Ostern fertigzumachen.

| Action | Owner | Timeline |
|--------|-------|----------|
| Roh-Dokumentation vorbereiten (Passwörter raus) | Ulrich | Über Ostern |

### Part 11: Angebotsautomatisierung — Portal Wilsch ⏳

*Hinzugefügt am 2. April.*

Thomas Erhard schreibt jedes Hosting-Angebot von Hand — viele Abschnitte wiederholen sich kundenübergreifend (#640). Drei bestehende Angebote liegen als PDFs vor (Hellmann, Holz Henkel, OSM). Ulrich zeigte die "Angebotsübersicht"-Vorlage per Bildschirmfreigabe — dasselbe Format, das auch in #1334 einfließt.

Hosting-Nachfrage ist aktuell allerdings eine Totenstille. Ulrich: auf Q4 / Jahresende verschieben. Sowohl Ulrich als auch Thomas haben angefangen, einige Angebote bereits über KI/Claude Code zu erstellen.

Hinweis: Die Angebotsübersicht-Vorlage und #1334 PDF-Angebotsgenerator (Part 7) teilen dasselbe Dokumentformat. #1334 bedient jetzt die DGX Spark Sales-Pipeline; #640 bedient das breitere Hosting-Portfolio später.

### Bonus: AWO / DGX-Setup

Das QSFP 400Gbit Kabel ist angekommen. Marius testet und installiert das Modell bis Dienstag/Mittwoch. AWO-Kundenmeetings wechseln zu einem 2-Wochen-Rhythmus; nächstes Meeting ~14. April nach technischer Validierung.

**2. April Update:** Hardware-Blocker aufgelöst — beide Maschinen kommunizieren mit 1ms Latenz, 256 GB RAM kombiniert, 200 Gbps Bandbreite. Nächster Schritt: Model-Sharding validieren (MiniMax M FP Int4 auf beide Maschinen aufteilen). Ziel: bis zum 10. April präsentierbar. Ulrich ruft diese Woche AWO-Kontakte an und schlägt den 10. April 13–15 Uhr als Meeting vor. Marius' strategisches Ziel: zweiwöchentlichen Rhythmus wieder etablieren.

| Action | Owner | Timeline |
|--------|-------|----------|
| AWO-Kontakte anrufen, 10. April Meeting vorschlagen | Ulrich | Diese Woche |
| Model-Sharding auf Zwei-Maschinen-Setup validieren | Marius | Vor 10. April |

### Bonus: Placetel-Ersatz

Frank Lasinski bietet eine Alternative zu €9,90/Person (vs. aktuell €55/Person). Er kann es für das gesamte Team einrichten. Thema abgeschlossen — Ulrich klärt mit Frank.

### Bonus: DGX Spark als Miet-Ressource

€500/Monat Mietmodell für Kunden, bereits aktiv mit AWO. Wird im Rekers Step 2 Angebot enthalten sein.

---

## Source

- **Transcripts (30. März):** [Transcript 1](https://app.fireflies.ai/view/01KMT67DGFRWFQABVPQXJS4TG8) · [Transcript 2](https://app.fireflies.ai/view/01KMZHD5KNYE528RKCYD02V9J8)
- **Transcripts (2. April):** [Transcript](https://drive.google.com/file/d/1obzukaxq0plaCuZJ7REe25rQ3gOrNdCG/view)
- **Agenda (30. März):** [Meeting-Agenda Ulrich Sync 30. März](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-ulrich-sync-2026-03-30)
- **Agenda (2. April):** [Meeting-Agenda Ulrich Sync 2. April](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-ulrich-sync-2026-04-02)
- **Tracking:** [#1312](https://github.com/DaveX2001/deliverable-tracking/issues/1312)
- **Session (30. März):** 8efe0543-2f51-483e-8c0c-f0184380ec4e
- **Session (2. April Update):** 838f67b9-e4c4-4d5d-a8a4-9128a5f72632

🤖

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

