---
publish: true
---

# Hosting-Anforderungen — ROHDEX Dokumentenverarbeitung
[[hosting-anforderungen-kunde]]

Technische Anforderungen und Hosting-Optionen für die Migration des ROHDEX-Dokumentenverarbeitungssystems.

---

## 1. Systemübersicht

Das System ist ein einzelner Docker-Container (FastAPI, Python 3.11+) ohne Datenbank. Zustandslose Verarbeitung — keine persistenten Daten auf dem Server.

**Verarbeitungspipeline:**
1. IMAP-Polling holt E-Mails mit Excel-Anhängen (.xlsx, .csv) ab
2. Datenextraktion: Skriptbasierte Verarbeitung (deterministisch)
3. Berechnungen: Python-basierte Kalkulationslogik
4. Dokumentgenerierung: 9 Excel-Vorlagentypen (Mail-Avis, VGM, Packliste, etc.)
5. SMTP-Versand: Fertige Dokumente per E-Mail zurück an Absender

**Externe Abhängigkeiten:**
- E-Mail-Konto mit IMAP- und SMTP-Zugang (einzige Pflicht-Abhängigkeit)
- Fehlermonitoring (optional)
- Keine KI-API-Aufrufe im laufenden Betrieb

**Systemverhalten:**
- Polling-Intervall: ~10 Sekunden (konfigurierbar)
- Geringe CPU/RAM-Last — reine Dateiverarbeitung
- Kein ausgehender Internet-Traffic außer E-Mail und optionalem Monitoring

---

## 2. Cloud-Hosting-Optionen

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

Beide Anbieter sind für diese Systemgröße mehr als ausreichend.

---

## 3. Eigenes Rechenzentrum (On-Premise)

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

**Nächster Schritt:** Server-Spezifikationen des hauseigenen Systems zusenden → Machbarkeit wird bewertet → konkreter Migrationsplan wird erstellt.

---

## 4. Migration

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

**Stundensatz Migration:** 90 EUR/Stunde

---

*Erstellt: 2026-02-21 | Ansprechpartner: Marius Wilsch (marius@wilsch-ai.com)*
