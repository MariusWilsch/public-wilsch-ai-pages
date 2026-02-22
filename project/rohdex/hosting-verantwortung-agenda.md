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

### 2. Laufende Verantwortungsgrenze zwischen Server- und App-Ebene
⏱️ 15 min

Das aktuelle SLA (525 EUR/Monat) deckt sowohl Infrastruktur als auch Applikationswartung ab. Bei Eigenhosting verschiebt sich die Infrastrukturverantwortung auf Rohdex — der SLA-Preis muss sich entsprechend anpassen.

- Server-Ebene: Uptime, OS-Updates, Backups, Netzwerk, Hardware
- App-Ebene: Container-Health, Monitoring, Code-Updates, Bugfixes, Template-Anpassungen
- Monitoring (GlitchTip) liegt aktuell bei Marius — Zuständigkeit bei Eigenhosting offen
- Je mehr Rohdex-IT übernimmt, desto geringer der monatliche SLA-Betrag

**To resolve:** Die Trennlinie zwischen Server-Verantwortung (Rohdex-IT) und App-Verantwortung (Marius) definieren — insbesondere für Monitoring und Störungserkennung, da dies den SLA-Preis direkt beeinflusst.

## Meeting Format

- **Type:** Discovery — Telefongespräch mit Konstantin
- **Expectation:** Konstantin klärt vorab mit seinem IT-Dienstleister, welche Aufgaben die IT übernehmen kann
- **Outcome:** Klare Zuständigkeitsmatrix für Migration und laufenden Betrieb → SLA-Preisanpassung kann finalisiert werden

## Related

- **Design Doc:** [Hosting-Anforderungen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen)
- **Issue:** [#766 Client Communication Transition](https://github.com/DaveX2001/deliverable-tracking/issues/766)
- **SLA v3:** [Google Doc](https://docs.google.com/document/d/1zgJA0ToNX3GU0IoyM0yjnmlp4PV0cqS2LQTRjtpJg_U/edit)
