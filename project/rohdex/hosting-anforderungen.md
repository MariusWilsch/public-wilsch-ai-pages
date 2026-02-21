---
publish: true
---

# Hosting-Anforderungen — ROHDEX Dokumentenverarbeitung
[[hosting-anforderungen]]

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
| Festplatte | 40 GB SSD | 80 GB SSD |
| Betriebssystem | Linux (Ubuntu 22.04 LTS) | — |
| Container-Runtime | Docker Engine 24.x+ | — |
| Netzwerk ausgehend | IMAP (993), SMTP (587/465) | — |
| Netzwerk eingehend | Port 9000 (intern/VPN) | — |
| Internet | Business-Leitung, >10 Mbit/s | — |
| Stromversorgung | USV empfohlen | — |

**Risikofaktoren:**
- IMAP/SMTP-Ports werden häufig durch Firmen-Firewalls blockiert — IT muss ausgehende Ports prüfen und freigeben
- 24/7-Verfügbarkeit abhängig von eigener Wartungsdisziplin
- Keine automatischen Backups ohne explizite Backup-Strategie

**Nächster Schritt:** Konstantin sendet die Server-Spezifikationen seines hauseigenen Systems → Marius bewertet Machbarkeit → konkreter Migrationsplan wird erstellt.

### Part 4: Migration und E-Mail-Konfiguration

**Migrationsaufwand Cloud:** 1 Arbeitstag (8 Stunden)
- Docker-Container deployen und konfigurieren
- Umgebungsvariablen setzen (E-Mail-Zugangsdaten, Monitoring)
- E-Mail-Konten einrichten und testen (IMAP + SMTP)
- DNS/SSL-Konfiguration falls erforderlich
- Smoke-Tests und Funktionsüberprüfung

**Migrationsaufwand On-Premise:** Wird nach Erhalt der Server-Specs konkretisiert. Zusätzlich zum Cloud-Aufwand:
- Firewall-Konfiguration in Abstimmung mit IT
- Netzwerk-Freigaben (IMAP/SMTP-Ports ausgehend)
- Puffer für infrastrukturspezifische Besonderheiten

**E-Mail-Konfiguration:**
Das System benötigt ein dediziertes E-Mail-Konto mit IMAP- und SMTP-Zugang.

- IMAP: Eingangsverarbeitung (Polling alle ~10 Sekunden)
- SMTP: Versand der fertigen Dokumente an Absender
- Ordnerstruktur wird automatisch angelegt: `Processed`, `Skipped`
- Aktuell konfiguriert für Gmail und IONOS — weitere Provider möglich

**Stundensatz Migration:** 90 EUR/Stunde (wie besprochen)

**Undefined:** SLA-Modellanpassung nach Hosting-Entscheidung — wenn Rohdex die Infrastrukturkosten selbst trägt, verändert sich die Kostenstruktur des bestehenden SLA. → Nächster Extraktionspass

**Undefined:** Codebase-Bereinigung — nicht genutzter KI-Extraktionspfad (OpenRouter/Langfuse) und zugehörige Dokumentation aufräumen. → Nächster Extraktionspass

---

## Source

- **Transcript:** [Rohdex Call — Hosting Requirements (Feb 18, 10 min)](https://app.fireflies.ai/view/01KHRFX1ESBZ7CJ8Z4X69CS4XK) — Konstantin/Marius, Anforderungen und Hosting-Optionen besprochen
- **Community Research:** [LowEndTalk OVH vs Hetzner](https://lowendtalk.com/discussion/209375/does-ovh-vps-beat-hetzner-cloud-vps), [HN Discussion](https://news.ycombinator.com/item?id=45480878)
- **Production Validation:** Server logs (91.99.74.207) — 7 Emails, alle Excel-only, kein AI-Pfad aktiv
- **Code Analysis:** `processing_orchestration_service.py:344` — DOCX/AI-Pfad existiert aber nie getriggert
- **Issue:** [#766 Client Communication Transition](https://github.com/DaveX2001/deliverable-tracking/issues/766)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/dc52c659-0c63-41ea-8af8-6183d2a72867.jsonl
