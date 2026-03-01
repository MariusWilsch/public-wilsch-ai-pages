---
publish: true
---

# IITR Wartungsvertrag — Vorbereitung Ulrich

[[client-iitr]]

## Meetingziel

Zwei offene Wartungsvertragspunkte klären, bevor die finanziellen Rahmenbedingungen an IITR gehen:

1. **Scope-Grenze** — welche Infrastrukturebenen Marius abdeckt vs. Primeline/Hetzner
2. **Wochenend-Preisgestaltung** — ob erweiterte Erreichbarkeit angeboten wird und zu welchem Preis

Beide Entscheidungen betreffen direkt das Wartungsvertragsdokument. Ohne sie sind SLA-Umfang
und Bereitschaftspauschale unvollständig.

## Pre-Read

- [IITR Vertragsrahmen — Kommerzielle Rahmenbedingungen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-contract-extension-framework) — Preisentscheidungen bereits getroffen (120 EUR/h, 300 EUR Bereitschaft, Festpreis pro Projekt). Fokus auf die zwei **Undefined**-Markierungen in Abschnitt 3.
- [CTS Solutions — IT-SLA-Preisliste](https://cts-solutions.at/edv-preisliste-wartungsvertrage-slas/) — Referenzpreise für erweiterte und Wochenend-SLA-Stufen

---

## Diskussionsthemen

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Betreuungs-Scope und Haftungsgrenze
⏱️ 10 Min.

Der Wartungsvertrag sagt "dedizierte Betreuung unseres Servers" — aber es existieren vier
Infrastrukturebenen, jede mit einem anderen Verantwortlichen:

| Ebene | Aktueller Eigentümer | Marius' Rolle |
|-------|---------------------|---------------|
| **Applikation** (KI-Code, Docker, Ollama) | Marius | Baut und wartet |
| **Middleware** (OS-Dienste, Netzwerk) | Unklar | Verwaltet in der Praxis |
| **Hardware** (Staging: Hetzner Bare Metal; Produktion: Primeline) | Hetzner / Primeline | Kein Eigentum |
| **Netzwerk** (Hetzner 99% SLA; Primeline 4h Reaktion Mo–Fr) | Hetzner / Primeline | Keine Kontrolle |

- Marius' aktueller Ansatz: alles übernehmen, was er kann, eskalieren was seine Fähigkeiten übersteigt
- Primeines Produktionsgarantie deckt Hardware-Austausch ab (36 Monate, 4h Reaktionszeit) — aber nur Mo–Fr
- Wenn Primeline bei einem Hardware-Ausfall langsam reagiert, ist IITR offline. Die Vertragssprache bestimmt, ob das Marius' Haftung ist oder nicht
- Ulrich hat das im Gespräch am 27. Feb. als "riesiges Risiko" eingestuft, wenn der Vertrag keine klare Grenze zieht

**Zu klären:** Die vertragliche Haftungsgrenze — für welche Ebenen Marius verantwortlich ist, und explizite Ausschlusssprache für Ausfälle der Drittanbieter-Infrastruktur.

### 2. Wochenend-SLA-Preisgestaltung
⏱️ 10 Min.

Der aktuelle Wartungsvertrag sieht 24h Reaktionszeit während deutscher Geschäftszeiten vor
(Mo–Fr 8–18 Uhr MEZ). Worst Case: IITR meldet einen Ausfall Freitag um 17:01, Marius
antwortet Montag um 17:00 — eine 72-Stunden-Lücke.

Marius arbeitet in der Praxis jeden Tag, daher ist eine erweiterte Erreichbarkeit operativ
machbar. Die Frage ist, wie sie bepreist wird.

| Stufe | Reaktionsfenster | Marktpreis (einzelner Server) | Aktueller Vorschlag |
|-------|-----------------|------------------------------|-------------------|
| **Geschäftszeiten** (Mo–Fr 8–18) | 24h innerhalb Geschäftstagen | 300–330 EUR/Monat | 300 EUR ✓ |
| **Erweiterte Zeiten** (Mo–Fr 7–20) | 24h innerhalb erweiterter Zeiten | ~450 EUR/Monat | — |
| **5/24** (Mo–Fr, jede Stunde) | 24h jeder Werktag | ~690 EUR/Monat | — |
| **7/24** (jeden Tag, jede Stunde) | 24h jeder Tag | ~890 EUR/Monat | — |

- Marktdaten: CTS Solutions Austria veröffentlichte SLA-Stufen pro Linux-Server
- Bei 120 EUR/h entspricht die aktuelle 300 EUR Bereitschaftspauschale 2,5 Stunden — ein einzelner Wochenend-Vorfall könnte das aufbrauchen
- Marius ist bereit, möchte aber dass der Preis das persönliche Engagement widerspiegelt

**Zu klären:** Welche Erreichbarkeitsstufe IITR angeboten wird und die entsprechende Bereitschaftspauschale — Balance zwischen Marius' Bereitschaft und fairer Vergütung für erweiterte Abdeckung.

## Meeting-Format

- **Art:** Entscheidungsgespräch (Marius × Ulrich)
- **Dauer:** 20 Min.
- **Erwartung:** Ulrich liest vorher das Design-Dokument zu den kommerziellen Rahmenbedingungen — Preisentscheidungen stehen, nur die zwei offenen Punkte brauchen seinen Input
- **Ergebnis:** Zwei Entscheidungen, die den Wartungsvertrag-Scope und die Preisgestaltung vervollständigen — bereit, das Vertragsdokument für Eckhard zu erstellen

## Verknüpfungen

- **Issue:** [#954 — Prepare extension contract for signing](https://github.com/DaveX2001/deliverable-tracking/issues/954)
- **Design Doc:** [IITR Contract Extension — Commercial Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-contract-extension-framework)
- **Transkript:** [Marius × Ulrich — Contract Extension Pricing Strategy (27. Feb.)](https://app.fireflies.ai/view/01KJEVQ0Q6XWZNZP9P1FTPB2VW)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/9f14b021-2a4f-4da9-8925-b30c9eb7d426.jsonl
