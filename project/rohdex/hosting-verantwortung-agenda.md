---
publish: true
---

# Rohdex Hosting — Verantwortungsklärung
[[client-rohdex]]

## Meeting Goal

Zwei offene Verantwortungsfragen klären, die den SLA-Preis und den Migrationsablauf bestimmen:

1. **Infrastruktur-Konfiguration** — Wer richtet die Server-Umgebung ein (Docker, Firewall, Ports)?
2. **Laufende Verantwortungsgrenze** — Wo endet Marius' Zuständigkeit (App) und wo beginnt Rohdex-IT (Server)?

## Pre-Read

- [Hosting-Anforderungen — ROHDEX Dokumentenverarbeitung](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen-kunde) — Abschnitt 3 (On-Premise) und Abschnitt 4 (Migration) als Kontext für die Diskussionspunkte

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Infrastruktur-Konfiguration bei On-Premise-Migration
⏱️ 10 min

Für die Migration auf Rohdex-Infrastruktur muss Docker installiert, Firewall-Ports geöffnet und Netzwerk-Konnektivität hergestellt werden. Aktuell ist nicht definiert, wer diese Arbeiten durchführt.

- Rohdex-IT hat eigene Serverinfrastruktur und verwaltet diese eigenständig
- Marius benötigt SSH-/Remote-Zugang für das App-Deployment
- Eine IT-Checkliste mit Vorbereitungsschritten existiert bereits im Anforderungsdokument

**To resolve:** Die Zuständigkeit für die Infrastruktur-Vorbereitung festlegen — entweder übernimmt Marius die gesamte Konfiguration (mit Zugang von Rohdex-IT), oder Rohdex-IT bereitet die Umgebung nach Checkliste vor und Marius deployt nur die Applikation.

### 2. SLA-Preismodell — Verantwortung bestimmt den Preis, nicht der Standort
⏱️ 15 min

Das aktuelle SLA (525 EUR/Monat) setzt sich aus zwei Bereichen zusammen: App-Verantwortung (325 EUR) und Infrastruktur-Verantwortung (200 EUR). Der Preis hängt davon ab, wer den Server managt — nicht davon, wo der Server steht.

- App-Ebene (325 EUR): Wartung, Updates, Bugfixes, Monitoring (GlitchTip), Template-Anpassungen — bleibt bei Marius
- Infra-Ebene (+200 EUR): Server-Uptime, OS-Updates, Backups, Netzwerk, Docker-Engine — nur wenn Marius verantwortlich
- Marius managt Server (Cloud oder On-Premise) = 525 EUR
- Rohdex-IT managt Server (Cloud oder On-Premise) = 325 EUR

**To resolve:** Festlegen, wer die Server-Verantwortung übernimmt — unabhängig vom Hosting-Standort. Damit steht der SLA-Preis fest.

## Meeting Format

- **Type:** Discovery — Telefongespräch mit Konstantin
- **Expectation:** Konstantin klärt vorab mit seinem IT-Dienstleister, welche Aufgaben die IT übernehmen kann
- **Outcome:** Klare Zuständigkeitsmatrix für Migration und laufenden Betrieb → SLA-Preisanpassung kann finalisiert werden

## Related

- **Design Doc:** [Hosting-Anforderungen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen)
- **Issue:** [#766 Client Communication Transition](https://github.com/DaveX2001/deliverable-tracking/issues/766)
- **SLA v3:** [Google Doc](https://docs.google.com/document/d/1zgJA0ToNX3GU0IoyM0yjnmlp4PV0cqS2LQTRjtpJg_U/edit)
