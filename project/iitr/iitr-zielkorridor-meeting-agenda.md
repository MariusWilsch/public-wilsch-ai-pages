---
publish: true
---

# IITR Zielkorridor — Meeting-Agenda

[[client-iitr]]

## Meetingziel

Die direkte Zusammenarbeit zwischen Marius Wilsch und IITR Datenschutz GmbH definieren — Taktung, Umfang und gegenseitige Erwartungen.

1. **DS-Kit Lieferstatus** — aktuelle Testergebnisse und Live-Demo vorstellen, Lieferzeitplan bestätigen
2. **Zweiwöchentliche Taktung vereinbaren** — Festpreis-Meilensteinlieferung mit regelmäßigen zweiwöchentlichen Berührungspunkten als Zusammenarbeitsmodell vorschlagen
3. **Portfolio-Abgleich** — alle aktiven Systeme (RAG-V1, RAG-V2, DS-Kit, Avatar) durchgehen und Prioritäten für nächste Meilensteine abstimmen
4. **Serveradministration** — formelle Übernahme der Infrastrukturverantwortung

## Pre-Read

- [DS-Kit Test-Analyse](dskit-rag-test-analysis.md) — aktueller Genauigkeits- und Lückenstatus
- [DS-Kit Festpreisangebot](dskit-navigation-rag-estimate.md) — ursprünglicher Umfang und Preisgestaltung
- [Kraska-E-Mail (17. Feb)](https://mail.google.com/mail/u/0/#all/19c6ae56b934ed26) — ursprüngliche Anfrage zu Zielkorridoren

---

## Diskussionsthemen

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Verzögerte Antworten und Verantwortungsübernahme
⏱️ 5 Min.

Mehrere E-Mails sind unbeantwortet, darunter die SpaceNet VPN-Migrations-Rückfrage (20. Feb). Die Verzögerung hängt mit einer Übergangsphase bezüglich des bisherigen Vermittlers zusammen.

- SpaceNet wartet auf Rückmeldung zum VPN/SSL-Migrationsstatus ([E-Mail](https://mail.google.com/mail/u/0/#all/19c79f0c8b9388a8))
- Ab sofort: Marius ist der einzige Ansprechpartner für alle technischen Themen — keine Vermittlerverzögerungen mehr
- Dieses Meeting etabliert die Arbeitsbeziehung, die diese Zusage umsetzbar macht

**Zu klären:** Verzögerung anerkennen, direkte Verantwortungsübernahme für Kommunikation und Reaktionsfähigkeit zusagen.

### 2. DS-Kit Navigation: Aktueller Stand und Live-Demo
⏱️ 10 Min.

Das DS-Kit-Navigationssystem steht kurz vor der Fertigstellung. Ein Test-Harness validiert Antworten gegen erwartete Ergebnisse mit Quellenattribution. Human-Witness-Testing läuft.

- Test-Rubrik: Frage, erwartete Antwort, erwartete Quelle vs. tatsächliche RAG-Antwort und Top-3 abgerufene Quellen
- Live-Demo von 2–3 repräsentativen Fragen mit Antwortqualität und Referenzgenauigkeit
- Verbleibende Lücken in der Testanalyse identifiziert — datenabhängig, nicht systemabhängig

**Zu klären:** Validierung, dass das System den Erwartungen entspricht, und Bestätigung des Lieferzeitplans.

### 3. IITR-Systemportfolio: Status und Prioritäten
⏱️ 10 Min.

Vier Systeme existieren im IITR-Portfolio. Jedes befindet sich in einem anderen Stadium — die Prioritäten von Kraska bestimmen, wohin der Aufwand als nächstes fließt.

- **RAG-V1** (DSGVO FAQ): Produktion, 85% Automatisierung, 97% Genauigkeit. Wartungsmodus. Langfuse-Monitoring vorhanden — Verbesserungen als laufender Umfang verfügbar
- **RAG-V2** (Rechtsprechungssuche): Gebaut, 2.567 Gerichtsurteile indiziert. Zitierextraktion und semantische Suche funktionsfähig
- **DS-Kit Navigation**: Kurz vor Fertigstellung (siehe Thema 2)
- **Avatar**: Konvergenzpunkt — einheitliche Oberfläche über alle drei RAG-Systeme. Aktueller Stand und IITRs Vision dafür müssen abgestimmt werden

**Zu klären:** Kraskas Prioritäten über das Portfolio verstehen und wo die Avatar-Konvergenz aus ihrer Sicht steht.

### 4. Vorgeschlagenes Arbeitsmodell: Zweiwöchentliche Festpreis-Taktung
⏱️ 10 Min.

Ein strukturiertes Zusammenarbeitsmodell ersetzt das bisherige Ad-hoc-Kommunikationsmuster. Jeder Zweiwochenzyklus hat einen klaren Rhythmus: Fortschritt des letzten Zyklus zeigen, Umfang für den nächsten definieren.

- **Taktung:** Zweiwöchentliche Videotermine — fester Zeitplan, nicht „wenn etwas fertig ist"
- **Preisgestaltung:** Festpreis pro Liefergegenstand, in jedem Meeting abgestimmt und vereinbart. Abrechnung bei Lieferung
- **Zyklusstruktur:** Erledigtes besprechen → nächsten Meilenstein vereinbaren → vor dem nächsten Meeting liefern
- **Ad-hoc:** Bei Klärungsbedarf zwischen den Zyklen kurze Ad-hoc-Gespräche möglich — primärer Kontakt über Stellmacher
- **Beispielumfang pro Zyklus:** Langfuse-Monitoring-Verbesserungen, DS-Kit-Iterationen nach Feedback, Avatar-Fortschritt

**Zu klären:** Einigung auf die zweiwöchentliche Taktung als Zusammenarbeitsrahmen und Startdatum des ersten Zyklus.

### 5. Serveradministration und Wartungsvereinbarung
⏱️ 10 Min.

Die Serverinfrastruktur wird bereits von Marius betrieben — die formelle Eigentumsübernahme von Rolnik ist der logische nächste Schritt. Die SpaceNet VPN-Migration (20. Feb) ist ein sofortiger Handlungspunkt.

- Serveradministration derzeit unter Rolniks Namen, von Marius betrieben
- SpaceNet VPN/SSL-Migration wartet auf Antwort — Marius übernimmt die Verantwortung sofort
- Wartungs-SLA: separate Vereinbarung für Systemverfügbarkeit, Updates und Monitoring aller Produktivsysteme (RAG-V1, RAG-V2, DS-Kit nach Lieferung)
- SLA ist unabhängig von Projektarbeit — deckt laufende Systemgesundheit ab

**Zu klären:** Formelle Übertragung der Serveradministration und Abstimmung über den Umfang eines Wartungs-SLAs.

## Meeting-Format

- **Art:** Videocall (Zoom)
- **Datum:** Dienstag, 24. Februar 2026, 14:30 Uhr
- **Dauer:** 30–45 Minuten
- **Teilnehmer:** Eckehard Kraska, Sebastian Kraska, Marvin Stellmacher, Marius Wilsch
- **Zoom:** Meeting-ID 852 0098 1719, Kenncode 242060
- **Erwartung:** Marius stellt DS-Kit-Demo und Zielkorridor-Vorschlag vor; Kraskas geben Prioritäten und Feedback zum Arbeitsmodell
- **Ergebnis:** Einigung auf zweiwöchentliche Taktung, Klarheit über Portfolio-Prioritäten, Serveradministrations-Übergabe eingeleitet

## Verknüpfungen

- **Issue:** [#766 — Client Communication Transition](https://github.com/DaveX2001/deliverable-tracking/issues/766)
- **Kraska-E-Mail:** [Terminanfrage (17. Feb)](https://mail.google.com/mail/u/0/#all/19c6ae56b934ed26)
- **SpaceNet-E-Mail:** [VPN-Migration (20. Feb)](https://mail.google.com/mail/u/0/#all/19c79f0c8b9388a8)
- **Vertragsstrategie:** [#847 Design Doc](https://github.com/DaveX2001/deliverable-tracking/issues/847)
- **DS-Kit-Angebot:** [Festpreisangebot](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-rag-estimate)

---

*Quelle: Extraktionspass vom 24. Februar 2026. Session: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/a79da5f4-1884-450e-8e7d-bc430a975b6f.jsonl*
