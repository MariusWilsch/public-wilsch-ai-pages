---
publish: true
---

# Hosting-Anforderungen — ROHDEX Dokumentenverarbeitung
[[client-rohdex]]

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
| Festplatte | 40 GB (SSD empfohlen, HDD ausreichend) | 80 GB |
| Betriebssystem | Linux-basiert (Ubuntu empfohlen) | — |
| Container-Runtime | Docker Engine 24.x+ | — |
| Netzwerk ausgehend | IMAP (993), SMTP (587/465) | — |
| Netzwerk eingehend | Port 9000 (intern/VPN) | — |
| Internet | Business-Leitung, >10 Mbit/s | — |
| Stromversorgung | USV empfohlen | — |

**Machbarkeitsbewertung (Stand Feb 2026):**
Die von Rohdex-IT angebotenen Spezifikationen (4 V-Cores, 16 GB RAM, 120 GB Speicher) übertreffen alle Mindestanforderungen — On-Premise-Hosting ist aus Hardware-Sicht machbar.

**Vorbereitung durch Rohdex-IT (vor Migrationstermin):**
1. Ausgehende Ports freigeben: IMAP (993) und SMTP (587 oder 465)
2. SSH-Zugang oder Remote-Zugriff für Marius einrichten

Alle weiteren Schritte (Docker-Installation, Konnektivitätstest, App-Deployment) übernimmt Marius nach Erhalt des Zugangs.

**Risikofaktoren:**
- 24/7-Verfügbarkeit abhängig von eigener Wartungsdisziplin
- Keine automatischen Backups ohne explizite Backup-Strategie

---

## 4. Migration

**Migrationsaufwand:** 1 Arbeitstag (8 Stunden) — gilt für beide Hosting-Optionen.

| Aufgabe | Stunden |
|---------|---------|
| Docker-Container deployen und konfigurieren | ~1,5h |
| Umgebungsvariablen setzen (E-Mail, Monitoring) | ~0,5h |
| E-Mail-Konnektivität verifizieren (IMAP + SMTP) | ~0,5h |
| DNS/SSL-Konfiguration falls erforderlich | ~0,5h |
| Funktionstest aller 9 Verarbeitungsschritte | ~2h |
| Inbetriebnahme und Übergabe | ~1h |
| Puffer für Unvorhergesehenes | ~2h |
| **Gesamt** | **~8h** |

Bei On-Premise-Migration: zusätzliche Kalenderzeit für IT-Koordination (Firewall-Freigaben, Zugangsvorbereitung) — diese fällt vor dem Migrationstermin an, nicht am Migrationstag selbst.

**E-Mail-Konfiguration:**
Das System nutzt bereits das Rohdex-eigene E-Mail-Konto `export-ki@rohdex.com` (IONOS). Dieses Konto bleibt bei der Migration identisch — es werden lediglich die bestehenden Zugangsdaten auf dem neuen Server hinterlegt.

- IMAP: Eingangsverarbeitung (Polling alle ~10 Sekunden) — Server: `imap.ionos.de` (Port 993, TLS)
- SMTP: Versand der fertigen Dokumente — Server: `smtp.ionos.de` (Port 587, TLS)
- Ordnerstruktur wird automatisch angelegt: `Processed`, `Skipped`

**Migrationskosten:** 720 EUR (8 Stunden × 90 EUR/Stunde)

---
