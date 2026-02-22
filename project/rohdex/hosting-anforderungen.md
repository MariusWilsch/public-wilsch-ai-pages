---
publish: true
---

# Hosting-Anforderungen — ROHDEX Dokumentenverarbeitung
[[client-rohdex]]

Technische Anforderungen und Hosting-Optionen für die Migration des ROHDEX-Dokumentenverarbeitungssystems. Zielgruppe: IT-Dienstleister zur Machbarkeitsprüfung.

---

## Problem Statement

Das ROHDEX-Dokumentenverarbeitungssystem läuft aktuell auf der Infrastruktur von Wilsch AI Services (Hetzner VPS). Rohdex übernimmt die direkte Geschäftsbeziehung und benötigt eine eigene Hosting-Lösung — entweder Cloud-basiert oder im eigenen Rechenzentrum.

Konstantin (Rohdex) muss seinem IT-Dienstleister ein Anforderungsdokument vorlegen, das die technischen Mindestanforderungen und Hosting-Optionen beschreibt, damit dieser die Machbarkeit bewerten kann.

**Preconditions:**
- Geschäftsbeziehung läuft ab sofort direkt zwischen Marius und Rohdex (kein Intermediär)
- SLA v3 (525 EUR/Monat) ist via SignNow zur Unterschrift versendet
- System ist produktiv und verarbeitet aktiv Versanddokumente
- Konstantin hat keine technische Erfahrung mit Hosting ("komplett Laien")
- Rohdex hat einen internen IT-Dienstleister mit eigener Serverinfrastruktur

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Konstantin kann seinem IT-Dienstleister ein Dokument vorlegen, das alle technischen Anforderungen und Hosting-Optionen enthält — ohne Rückfragen an Marius |
| **Success** | IT-Dienstleister bestätigt Machbarkeit für mindestens eine Option und Rohdex trifft eine Hosting-Entscheidung |
| **Done test** | Kann Konstantin das Dokument weiterleiten und erhält eine ja/nein-Antwort von der IT? Wenn ja → Dokument ist vollständig |

---

## Approach

### Part 1: Systemarchitektur

Das System ist ein einzelner Docker-Container (FastAPI, Python 3.11+) ohne Datenbank. Zustandslose Verarbeitung — keine persistenten Daten auf dem Server.

**Verarbeitungspipeline:**
1. IMAP-Polling holt E-Mails mit Excel-Anhängen (.xlsx, .csv) ab
2. Datenextraktion: Skriptbasierte Verarbeitung (openpyxl/csv — deterministisch)
3. Berechnungen: Python-basierte Kalkulationslogik
4. Dokumentgenerierung: 9 Excel-Vorlagentypen (Mail-Avis, VGM, Packliste, etc.)
5. SMTP-Versand: Fertige Dokumente per E-Mail zurück an Absender

**Externe Abhängigkeiten:**
- E-Mail-Konto mit IMAP- und SMTP-Zugang (einzige Pflicht-Abhängigkeit)
- GlitchTip/Sentry für Fehlermonitoring (optional)
- Keine KI-API-Aufrufe im laufenden Betrieb (validiert anhand Produktionslogs)

**Systemverhalten:**
- Polling-Intervall: ~10 Sekunden (konfigurierbar)
- Geringe CPU/RAM-Last — reine Dateiverarbeitung
- Kein ausgehender Internet-Traffic außer E-Mail und optionalem Monitoring

### Part 2: Cloud-Hosting-Optionen

Zwei Cloud-Anbieter mit Rechenzentren in Deutschland (DSGVO-konform):

**Empfehlung:** [OVHcloud VPS-1](https://www.ovhcloud.com/de/vps/) — ab ~€3,82/Mo (exkl. MwSt., 12-Mo-Vertrag, Stand Feb 2026)

**Alternative:** [Hetzner CX33](https://www.hetzner.com/cloud/) — ab ~€6,59/Mo (exkl. MwSt., inkl. Backup, Stand Feb 2026)

**Vergleich:**

| Kriterium | OVHcloud | Hetzner |
|-----------|----------|---------|
| Monatspreis | ~€3,82 | ~€6,59 (inkl. Backup) |
| Backups | Inklusive (täglich) | +€1,10/Mo |
| DDoS-Schutz | Inklusive | Aufpreis |
| Support | 24/7, Live-Chat | Geschäftszeiten, Tickets |
| Performance/Kern | Gut | Besser |
| Control Panel | Langsam (bekannt) | Schnell |

**Community-Konsens (LowEndTalk, HackerNews):**
- OVHcloud: Bestes Preis-Leistungs-Verhältnis, inkludierte Sicherheitsfeatures
- Hetzner: Höhere Zuverlässigkeit, bessere Admin-Erfahrung
- Beide für diese Systemgröße mehr als ausreichend

### Part 3: Eigenes Rechenzentrum (On-Premise)

**Mindestanforderungen:**

| Anforderung | Minimum | Empfohlen |
|------------|---------|-----------|
| CPU | 2 Kerne, x86-64 | 4 Kerne |
| RAM | 4 GB dediziert | 8 GB Gesamt-Host |
| Festplatte | 40 GB (SSD empfohlen, HDD ausreichend) | 80 GB |
| Betriebssystem | Linux-basiert (Ubuntu empfohlen) | — |
| Container-Runtime | Docker Engine 24.x+ | — |
| Netzwerk ausgehend | IMAP (993), SMTP (587/465) | — |
| Netzwerk eingehend | Port 9000 (intern/VPN) | — |
| Internet | Business-Leitung, >10 Mbit/s | — |
| Stromversorgung | USV empfohlen | — |

**Machbarkeitsbewertung (Stand Feb 2026):**
Rohdex-IT bietet: 4 V-Cores, 16 GB RAM, 120 GB Speicher. Diese Spezifikationen übertreffen alle Mindestanforderungen — On-Premise-Hosting ist aus Hardware-Sicht machbar.

**Vorbereitung durch Rohdex-IT (vor Migrationstermin):**
1. Ausgehende Ports freigeben: IMAP (993) und SMTP (587 oder 465)
2. SSH-Zugang oder Remote-Zugriff für Marius einrichten

**Abhängig von vereinbarter Verantwortung (Marius oder Rohdex-IT):**
3. Docker Engine 24.x+ auf der VM installieren und lauffähig bestätigen
4. Konnektivität prüfen: Von der VM aus `imap.ionos.de:993` und `smtp.ionos.de:587` erreichbar

**Risikofaktoren:**
- 24/7-Verfügbarkeit abhängig von eigener Wartungsdisziplin
- Keine automatischen Backups ohne explizite Backup-Strategie

**Undefined:** Wer konfiguriert die Infrastruktur auf Rohdex-Seite? Zwei Optionen — (A) Marius konfiguriert alles (Infrastruktur + App-Deployment), Rohdex-IT stellt Zugang bereit, oder (B) Rohdex-IT bereitet Infrastruktur nach obiger Checkliste vor, Marius deployt nur die Applikation. Antwort beeinflusst Migrationsaufwand und Zeitplanung. → Meeting Agenda

### Part 4: Migration und E-Mail-Konfiguration

**Migrationsaufwand Cloud:** 1 Arbeitstag (8 Stunden)
- Docker-Container deployen und konfigurieren
- Umgebungsvariablen setzen (E-Mail-Zugangsdaten, Monitoring)
- E-Mail-Konnektivität verifizieren (IMAP + SMTP)
- DNS/SSL-Konfiguration falls erforderlich
- Funktionstest und Überprüfung aller Verarbeitungsschritte

**Migrationsaufwand On-Premise:** 1 Arbeitstag (8 Stunden)
Identischer technischer Aufwand wie Cloud-Migration. Zusätzliche Kalenderzeit für IT-Koordination (Firewall-Freigaben, Zugangsvorbereitung) — diese fällt vor dem Migrationstermin an, nicht am Migrationstag selbst.

**E-Mail-Konfiguration:**
Das System nutzt bereits ein Rohdex-eigenes E-Mail-Konto: `export-ki@rohdex.com` (IONOS). Dieses Konto bleibt bei der Migration identisch — es werden lediglich die bestehenden Zugangsdaten auf dem neuen Server hinterlegt.

- IMAP: Eingangsverarbeitung (Polling alle ~10 Sekunden)
- SMTP: Versand der fertigen Dokumente an Absender
- Ordnerstruktur wird automatisch angelegt: `Processed`, `Skipped`
- IMAP-Server: `imap.ionos.de` (Port 993, TLS)
- SMTP-Server: `smtp.ionos.de` (Port 587, TLS)

**Migrationskosten:** 720 EUR (8 Stunden × 90 EUR/Stunde) — gilt für beide Hosting-Optionen.

### Part 5: SLA-Modell nach Hosting-Entscheidung

Das aktuelle SLA (525 EUR/Monat) beinhaltet sowohl die Applikationswartung als auch die Infrastrukturverantwortung. Bei Eigenhosting verschiebt sich die Verantwortung — der SLA-Preis passt sich entsprechend an.

| | Cloud (Marius hosted) | Eigenes Rechenzentrum |
|---|---|---|
| **Server-Verantwortung** | Marius | Rohdex-IT |
| **App-Wartung & Updates** | Marius | Marius |
| **Monitoring** | Marius | *zu klären* |
| **Bugfixes** | Marius | Marius |
| **Monatspreis** | 525 EUR | *nach Absprache — abhängig von Verantwortungsaufteilung* |

> *Bei Eigenhosting entfallen die Infrastrukturkosten auf Rohdex-Seite. Die SLA-Anpassung richtet sich danach, welche Verantwortlichkeiten Rohdex-IT übernimmt — je mehr Rohdex-IT abdeckt, desto geringer der monatliche SLA-Betrag.*

**Undefined:** Verantwortungsgrenze zwischen Server-Ebene und App-Ebene — bestimmt sowohl die SLA-Preisanpassung als auch die Monitoring-Zuständigkeit. Zu klären im nächsten Gespräch mit Konstantin und Rohdex-IT. → Meeting Agenda

---

## Source

- **Transcript:** [Rohdex Call — Hosting Requirements (Feb 18, 10 min)](https://app.fireflies.ai/view/01KHRFX1ESBZ7CJ8Z4X69CS4XK) — Konstantin/Marius, Anforderungen und Hosting-Optionen besprochen
- **Community Research:** [LowEndTalk OVH vs Hetzner](https://lowendtalk.com/discussion/209375/does-ovh-vps-beat-hetzner-cloud-vps), [HN Discussion](https://news.ycombinator.com/item?id=45480878)
- **Production Validation:** Server logs (91.99.74.207) — 7 Emails, alle Excel-only, kein AI-Pfad aktiv
- **Code Analysis:** `processing_orchestration_service.py:344` — DOCX/AI-Pfad existiert aber nie getriggert
- **Server Specs:** [Konstantin Email (Feb 18)](https://mail.google.com/mail/u/0/#all/19c715d69ebb2c67) — 4 V-Cores, 16 GB RAM, 120 GB Speicher
- **Email Config:** Production env (WILSCH-AI-SERVER) — `export-ki@rohdex.com` via IONOS (IMAP/SMTP)
- **Issue:** [#766 Client Communication Transition](https://github.com/DaveX2001/deliverable-tracking/issues/766)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/dc52c659-0c63-41ea-8af8-6183d2a72867.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/6aba464a-4eac-48dc-82c6-d5f78cd4d7e4.jsonl
