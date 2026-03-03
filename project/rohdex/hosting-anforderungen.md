---
publish: true
---

# Hosting-Anforderungen — ROHDEX Dokumentenverarbeitung
[[client-rohdex]]

Technische Anforderungen und Hosting-Optionen für die Migration des ROHDEX-Dokumentenverarbeitungssystems. Zielgruppe: IT-Dienstleister zur Machbarkeitsprüfung.

---

## Problem Statement

Das ROHDEX-Dokumentenverarbeitungssystem läuft aktuell auf der Infrastruktur von Wilsch AI Services (Hetzner VPS). Die Migration auf Rohdex-eigene Infrastruktur erfordert Koordination zwischen drei Parteien:

- **Rohdex (Konstantin Fitermann)** — Auftraggeber. Geschäftsbeziehung direkt mit Wilsch AI Services.
- **Gmelch IT-Systeme (Sikander Wenzel)** — IT-Dienstleister von Rohdex. Stellt Server-Infrastruktur bereit (VM, VPN, Backups, Netzwerk).
- **Wilsch AI Services (Marius Wilsch)** — Applikationsentwickler. Führt Migration durch, wartet das System gemäß SLA v4.

Gmelch IT stellt die Infrastruktur bereit und betreibt sie. Wilsch AI migriert die Applikation und wartet sie. Rohdex ist der Auftraggeber beider Leistungen.

**Preconditions:**
- Geschäftsbeziehung direkt zwischen Marius und Rohdex (kein Intermediär)
- SLA v4 countersigned (26.02.2026) — 90 EUR/h, keine Pauschale, 30-Minuten-Takte
- System produktiv auf WILSCH-AI-SERVER, verarbeitet aktiv Versanddokumente
- On-Premise-Entscheidung getroffen: Migration auf Rohdex-VM (RDX-APP-01)
- IT-Dienstleister: Gmelch IT-Systeme (Sikander Wenzel, sw@gmelch-itsysteme.de)
- VM bereitgestellt: 4 V-Cores, 16 GB RAM, 100 GB VHD (60 GB nutzbar), Docker installiert

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | System migriert auf RDX-APP-01, bekannte Bugs gefixt, alle Dokumenttypen funktional — Wartung läuft unter SLA v4 |
| **Success** | System produktiv auf Rohdex-Infrastruktur, kein Traffic mehr über Wilsch AI Server |
| **Done test** | E-Mails werden auf RDX-APP-01 verarbeitet und Dokumente korrekt zurückgesendet? Wenn ja → Migration abgeschlossen |

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

### Part 2: On-Premise-Infrastruktur (Rohdex / Gmelch IT)

**Bereitgestellte Infrastruktur (Gmelch IT-Systeme, Stand 26.02.2026):**

| Eigenschaft | Wert |
|------------|------|
| Servername | RDX-APP-01 |
| Interne IP | 192.168.44.11 |
| CPU | 4 V-Cores |
| RAM | 16 GB |
| Festplatte | 100 GB VHD (60 GB nutzbar nach Ubuntu-Installation) |
| Betriebssystem | Ubuntu Server |
| Docker | Neueste Version installiert |
| Backups | Täglich ab 22:00 (VM-Level, durch Gmelch) |
| Ausgehend | Sicherheitskritische Anwendungen + Länder (RU, CN etc.) gesperrt — IONOS-Mailserver (DE) nicht betroffen |

**Zugang:**
- **VPN:** WatchGuard SSL-VPN-Client → `217.92.100.123:443`
- **SSH:** Über VPN-Tunnel zu `192.168.44.11`
- **Credentials:** psst-Vault (global, Tag `rohdex`) — Details in Rohdex CLAUDE.md
- **Support:** Projektbezogen → Sikander Wenzel direkt. Supportanfragen → support@gmelch-itsysteme.de

**Verantwortungsteilung (geklärt durch SLA v4 §1.3):**
- **Gmelch IT:** Infrastruktur — Server-Uptime, OS-Updates, Backups, Netzwerk, Docker-Engine
- **Marius:** Applikationsebene — Deployment, Wartung, Monitoring, Updates, Bugfixes

### Part 3: Migration

**Migrationsaufwand:** 1 Arbeitstag (8 Stunden, 720 EUR) — gemäß SLA v4 §1.1

| Schritt | Aufwand |
|---------|---------|
| Docker-Container deployen und konfigurieren | ~1,5h |
| Umgebungsvariablen setzen (E-Mail, Monitoring) | ~0,5h |
| E-Mail-Konnektivität verifizieren (IMAP + SMTP) | ~0,5h |
| DNS/SSL-Konfiguration falls erforderlich | ~0,5h |
| Funktionstest aller 9 Verarbeitungsschritte | ~2h |
| Inbetriebnahme und Übergabe | ~1h |
| Bugfix Tara-Berechnung (→ Part 5, #655) | ~1h |
| Puffer für Unvorhergesehenes | ~1h |

**Deployment-Methode:** Repo auf Server klonen (`git clone`) + `make deploy`. Alternativ: pre-built image via `docker save/load` falls Git-Zugang von VM problematisch.

**Dead-Code-Cleanup (vor Migration):**
- AI-Pfad entfernen: `ai_data_extraction_service.py`, OpenRouter/Langfuse/Helicone Dependencies, DOCX-Handling
- GlitchTip/Sentry entfernen: `sentry-sdk[fastapi]`, kommentierter Init-Block, `/sentry-debug` Route, `SENTRY_DSN` Config
- Ungenutzte `.env`-Variablen: `OPENROUTER_API_KEY`, `HELICONE_API_KEY`, `LANGFUSE_SECRET_KEY`, `LANGFUSE_PUBLIC_KEY`, `SENTRY_DSN`

**E-Mail-Konfiguration (unverändert):**
- Konto: `export-ki@rohdex.com` (IONOS)
- IMAP: `imap.ionos.de:993` (TLS) — Polling alle ~60 Sekunden
- SMTP: `smtp.ionos.de:587` (TLS)
- Ordner: `Processed`, `Skipped` (automatisch angelegt)

### Part 4: SLA-Modell

**Superseded:** Retainer-Modell (525/325 EUR/Monat) durch SLA v4 ersetzt.

**Aktuelles Modell (SLA v4, signed 26.02.2026):**
- 90 EUR/h, 30-Minuten-Takte, keine Pauschale
- Infrastruktur: Rohdex-IT (Gmelch) — Server-Uptime, OS-Updates, Backups, Netzwerk, Docker
- Applikation: Marius — Wartung, Updates, Bugfixes, Template-Anpassungen
- Reaktionszeit: 1 Werktag (geschäftskritisch), Best-Effort (nicht-kritisch)

**Vollständiger Vertrag:** [SLA v4 Wartungsvertrag](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/sla-v4-wartungsvertrag)

### Part 5: Bekannte Probleme (Spec-Design durch Developer)

**#655: Tara-Berechnung — Dezimalstellen-Fehler in Packliste**

Packing List zeigt übermäßige Nachkommastellen bei Tara-Werten (z.B. `15.333333` statt `15.33`). Berechnete Gesamtsumme stimmt nicht mit Referenzwerten aus Log-Datei überein. Ursache: Rohe Python-Float-Arithmetik in `calculation_service.py` ohne Rundung vor Schreiben in Excel-Zellen. SLA v4 §1.1 allokiert ~1h für Fix.

**Datenquellen:** 📧 [Tara Problem Email (24.02.2026)](https://mail.google.com/mail/u/0/#all/19c8f8c66c05d43d) — Anhänge: `WAHRHEITSDATEI_2610023_.xlsx`, `Log_38302M.csv`, `PackingList_2610023.xlsx`, `Vorlage Packliste BUNDLES.xlsx`
**Code:** `app/services/data/calculation_service.py` (Zeilen 87–121), `app/services/document/strategies/PackingListExcelStrategy`
**Issue:** [#655](https://github.com/DaveX2001/deliverable-tracking/issues/655)

**#585: Veterinary BUSAN — Integrationsumfang definieren**

Konstantin sandte `Veterinary BUSAN.docx` (Beispiel-Dokument, 03.12.2025). VET-Dokumenttyp (`VetExcelStrategy`) existiert bereits im Code und generiert Veterinärzertifikate. Zu klären: Stimmt der aktuelle VET-Output mit dem Beispiel-Dokument überein?

**Undefined:** (A) VET-Template-Review — Vergleich des generierten VET-Outputs mit dem Beispieldokument `Veterinary BUSAN.docx`. Abweichungen dokumentieren. → Developer Spec-Design

**Undefined:** (B) Rezept/Material-Datenintegration — JSON-Datenstruktur vereinbart (Recipe ID → Materials Array → Material ID + Percentage + Ebenen/Vormischungen). 500MB+ Datenlieferung ausstehend. Beziehung zum VET-Dokument unklar. → Tiefere Analyse nötig

**Datenquellen:** 📧 [VETERINÄR Email (03.12.2025)](https://mail.google.com/mail/u/0/#all/19ae34999468ca08) — Anhang: `Veterinary BUSAN.docx`
**Code:** `app/services/document/strategies/VetExcelStrategy` (strukturell identisch mit DISPO)
**Issue:** [#585](https://github.com/DaveX2001/deliverable-tracking/issues/585)

---

## Source

- **Transcript:** [Rohdex Call — Hosting Requirements (Feb 18, 10 min)](https://app.fireflies.ai/view/01KHRFX1ESBZ7CJ8Z4X69CS4XK) — Anforderungen und Hosting-Optionen besprochen
- **Email Thread:** [Migration Programm ROHDEX (Feb 25-26)](https://mail.google.com/mail/u/0/#all/19c9530e9b21c06b) — Konstantin → Sikander → Marius: VM-Vorbereitung, VPN-Zugang, Server-Details
- **VPN Credentials:** [Keeper Link (Feb 26)](https://mail.google.com/mail/u/0/#all/19c9a93fce2727cf) — WatchGuard SSL-VPN
- **SSH Credentials:** [Keeper Link (Feb 26)](https://mail.google.com/mail/u/0/#all/19c9a955f43ac511) — Ubuntu-Benutzer RDX-APP-01
- **SLA v4:** [Wartungsvertrag (signed 26.02.2026)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/sla-v4-wartungsvertrag)
- **Tara Bug:** [Email (Feb 24)](https://mail.google.com/mail/u/0/#all/19c8f8c66c05d43d) — Input/Output-Daten zum Tara-Problem
- **Veterinary BUSAN:** [Email (Dec 3, 2025)](https://mail.google.com/mail/u/0/#all/19ae34999468ca08) — Beispiel-Dokument
- **Code Analysis:** Codebase at `~/Documents/projects/billable/ROHDEX/` — AI-Pfad nie getriggert (production validated)
- **Issue:** [#909 Dokumentenverarbeitung SLA & Wartung](https://github.com/DaveX2001/deliverable-tracking/issues/909) (Epic)
- **Sub-Issues:** [#961 Migration](https://github.com/DaveX2001/deliverable-tracking/issues/961), [#655 Tara Fix](https://github.com/DaveX2001/deliverable-tracking/issues/655), [#585 BUSAN](https://github.com/DaveX2001/deliverable-tracking/issues/585)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/eb15ce95-2336-43e6-baa8-a92e2e6b1adf.jsonl
