---
publish: true
---

# DS-Kit RAG Test Analysis - Question-by-Question Verification

Analysis date: 2026-01-13

---

## 1. Executive Summary

| Metric | Value |
|--------|-------|
| Total questions | 29 |
| Working | 17 (58.6%) |
| Data Gap | 9 |
| Ambiguous | 1 |
| VLM Data Loss | 1 |
| Q&A Data Error | 1 |
| **Current accuracy** | **17/29 (58.6%)** |
| **Max with current corpus** | **20/29 (69%)** |

**Key finding:** 9 of 12 failures are due to expected answers not existing in the indexed corpus. No retrieval tuning can fix questions whose answers aren't indexed.

---

## 2. Corpus Contents

### Indexed (Production - VLM + Q&A)
- **VLM chunks:** 56 documents (GraniteDocling VLM parsing)
- **Q&A pairs:** 14 documents (fragen-navigationssystem.csv)
- **Total:** 70 documents

### Available but not in production index
- **Standard Docling chunks:** 95 documents (includes PDF header with official contact)

### Key content in Q&A CSV (14 pairs)
1. Webseitendatenschutzerklärung → Kapitel 8
2. Textgeneratoren → FreeTools
3. E-Mail hinterlegen → dskit@iitr.de
4. Mitarbeiter Zugang → dskit@iitr.de
5. Magic-Link Login → passwortfrei/passwortgestützt
6. Schulung Häufigkeit → jährlich
7. Beratungsstunde → 180€ / DS-Kit Plus
8. Kündigung → 3 Monate
9. Datenschutzverpflichtung → Kapitel 09 (⚠️ should be 11)
10. Womit anfangen → Kapitel 02, 04, 05, 06, eLearning
11. AV-Verträge → Kapitel 05
12. Datenschutzverletzung → Kapitel 10
13. Auskunft Kunde → Kapitel 07
14. Audit → 360€

---

## 3. Question Analysis Table (All 29)

| Q# | Status | Question | Expected (key) | RAG Response (key) | In Corpus? | Category |
|----|--------|----------|----------------|-------------------|------------|----------|
| 1 | ✅ | Texte Webseitengestaltung | Kapitel 8 | "finden Sie...Kapitel 8" | ✅ | Working |
| 2 | ✅ | Textgeneratoren | FreeTools | "FreeTools auf Webseite" | ✅ | Working |
| 3 | ✅ | Andere E-Mail hinterlegen | dskit@iitr.de | "dskit@iitr.de" | ✅ | Working |
| 4 | ✅ | Zugang Unterlagen | Alle Vorlagen | "Zugang zu allen...Unterlagen" | ✅ | Working |
| 5 | ❌ | Schulungen eLearning | **eLearning + Statistik** | "mindestens einmal im Jahr" | ✅ | **Ambiguous** |
| 6 | ❌ | DSB auf Webseite | **"Ja, wenn bestellt"** | "Nein, Sie können nicht" | ❌ | **Data Gap** |
| 7 | ✅ | Beratungsstunde | 180€ / DS-Kit Plus | "180€ netto...Beratung" | ✅ | Working |
| 8 | ✅ | Audit | 360€ | "Audit...360€" | ✅ | Working |
| 9 | ✅ | Weiterer Mitarbeiter | dskit@iitr.de | "dskit@iitr.de" | ✅ | Working |
| 10 | ✅ | Zusätzliches Benutzerkonto | dskit@iitr.de | "DS-Kit-Team...dskit@" | ✅ | Working |
| 11 | ✅ | TISAX Schulungsinhalte | Kapitel 11 UP 3 | "Dokument 11-03" | ✅ | Working |
| 12 | ❌ | DSB Meldung wer | **"durch Verantwortlichen"** | "gemäß DS-Kit Vorgaben" | ❌ | **Data Gap** |
| 13 | ❌ | Wo DSB melden | **Kontaktdaten für Meldung** | "wird im Kontext nicht beantwortet" | ❌ | **Data Gap** |
| 14 | ❌ | DSB Kontaktdaten | **email@iitr.de, Marienplatz** | "dskit@iitr.de" (wrong) | ❌ | **VLM Loss** |
| 15 | ✅ | Kündigung Termin | 3 Monate | "drei Monate vor Vertragsende" | ✅ | Working |
| 16 | ❌ | eLearning 300€ | **"vier Module, 300€"** | "wenden Sie sich an Support" | ❌ | **Data Gap** |
| 17 | ❌ | Englisch Zertifikat | **"Ja, gerne ausstellen"** | "standardmäßig keine" (wrong) | ❌ | **Data Gap** |
| 18 | ❌ | Zugang DS-Kit URL | **"core.iitr.de"** | "wenden Sie sich an IITR" | ❌ | **Data Gap** |
| 19 | ✅ | Magic-Link wiederholt | passwortfrei/passwortgestützt | "Magic-Link...passwortgestützt" | ✅ | Working |
| 20 | ✅ | Jahresvertrag automatisch | Verlängert sich | "verlängert sich automatisch" | ✅ | Working |
| 21 | ❌ | Mitarbeiter Docs | **Kapitel 11 UP 2** | "Kapitel 9" (wrong chapter) | ✅ | **Q&A Error** |
| 22 | ✅ | Mitarbeiter einladen | Mitarbeiterschulung eLearning | "Mitarbeiterschulung...eLearning" | ✅ | Working |
| 23 | ✅ | Schulung Häufigkeit | jährlich | "mindestens einmal im Jahr" | ✅ | Working |
| 24 | ❌ | Video E-Mail Vereinbarung | **Kapitel 11 UP 3** | "wird nicht direkt...erwähnt" | ❌ | **Data Gap** |
| 25 | ✅ | Datenschutzverletzung | Kapitel 10 | "Kapitel 10...Interne Meldung" | ✅ | Working |
| 26 | ✅ | Datenschutzhinweise DE/EN | Kapitel 8 | "Kapitel 8...DS-Kit" | ✅ | Working |
| 27 | ❌ | Bewerber unterschreiben | **"nicht erforderlich"** | "in vielen Fällen" (wrong) | ❌ | **Data Gap** |
| 28 | ❌ | Cookie Banner 180€ | **"prüfen...180€"** | Gibt technische Beratung | ❌ | **Data Gap** |
| 29 | ✅ | Webseite Beratung | 180€ / DS-Kit Plus | "180€ netto...Beratung" | ✅ | Working |

---

## 4. Data Gap Details (9 questions)

### Q6: DSB auf Webseite
- **Question:** "Kann ich Sie als Datenschutzbeauftragter auf meiner Webseite angeben?"
- **Expected:** "Wenn Sie uns als Ihren externen Datenschutzbeauftragten bestellt haben, können Sie uns auf Ihrer Webseite sowie in Verträgen und bei der zuständigen Datenschutzaufsichtsbehörde mit den folgenden Kontaktdaten..."
- **Typesense:** Found 1 (Datenschutzverletzung content - wrong)
- **Verdict:** Add Q&A about DSB listing policy with full contact details

### Q12: DSB Meldung - Wer
- **Question:** "Übernehmen Sie die Meldung des externen Datenschutzbeauftragten?"
- **Expected:** "Die Meldung des Datenschutzbeauftragten bei der zuständigen Datenschutzaufsichtsbehörde muss durch den Verantwortlichen erfolgen..."
- **Typesense:** Found 5 (DSB Meldung document exists, but not WHO files)
- **Verdict:** Add Q&A clarifying customer must file, not IITR

### Q13: DSB Meldung - Wo
- **Question:** "Wo melde ich den externen Datenschutzbeauftragten?"
- **Expected:** Full IITR contact details for official DSB registration
- **Typesense:** Found 2 (Datenschutzverletzung - wrong content)
- **Verdict:** Add Q&A with official registration contact (email@iitr.de, Marienplatz address)

### Q16: eLearning Vollzugriff 300€
- **Question:** "Wie kann ich auf weitere eLearning Module zugreifen?"
- **Expected:** "Im Rahmen des Datenschutz-Kit Basis stehen Ihnen standardmäßig vier eLearning-Schulungsmodule zur Verfügung. Zu einem Aufpreis von 300 € netto jährlich..."
- **Typesense:** Found 0
- **Verdict:** Add Q&A about eLearning tiers and pricing

### Q17: Englische Zertifikate
- **Question:** "Stellen Sie auch englischsprachige Zertifikate zur Verfügung?"
- **Expected:** "Wir können Ihnen gerne ein englischsprachiges Datenschutz-Zertifikat ausstellen..."
- **Typesense:** Found 0
- **Verdict:** Add Q&A about English certificate availability

### Q18: Zugang URL
- **Question:** "Wie erhalte ich einen Zugang zum Datenschutz-Kit?"
- **Expected:** "...core.iitr.de aufrufen und unter Angabe Ihrer E-Mail-Adresse und Login-Link..."
- **Typesense:** Found 3 (Magic-Link Q&A - but no URL)
- **Verdict:** Add Q&A with actual login URL (core.iitr.de)

### Q24: E-Mail nach Beendigung
- **Question:** "In welchen Videos wird die Personal-Vereinbarung besprochen, dass E-Mails mit privatem Inhalt nicht verschickt werden dürfen..."
- **Expected:** "Eine Vorlage für eine Vereinbarung über den Zugriff auf die E-Mail-Adresse des Beschäftigten nach Beendigung des Beschäftigungsverhältnisses finden Sie in Kapitel 11, Unterpunkt 3..."
- **Typesense:** Found 2 (general email content - wrong)
- **Verdict:** Add Q&A about post-employment email access agreement location

### Q27: Bewerber unterschreiben
- **Question:** "Müssen unsere Bewerber unsere Datenschutzbestimmungen unterschreiben?"
- **Expected:** "Es ist nicht erforderlich, dass Bewerber Datenschutzhinweise unterschreiben. Bewerber sollten jedenfalls die Datenschutzhinweise zur Kenntnis nehmen."
- **Typesense:** Found 1 (Checkliste - wrong content)
- **Verdict:** Add Q&A about applicant data protection acknowledgment policy

### Q28: Cookie Banner Prüfung
- **Question:** "Ist das Cookie Banner, wie wir es derzeit auf unserer Website nutzen, so in Ordnung?"
- **Expected:** "Gerne prüfen wir Ihre Webseite auf Datenschutzkonformität zu unserem Stundensatz von 180€ netto..."
- **Typesense:** Found 0
- **Verdict:** Add Q&A about website review service and pricing

---

## 5. Issue Details (3 questions)

### Q5: Ambiguous Question (Schulungen)
- **Question:** "Kriegen wir auch Schulungen, damit wir unsere Mitarbeiter ab und zu mal beim Datenschutz auf den Stand bringen?"
- **Expected:** eLearning feature description
- **RAG answered:** Training frequency (yearly)
- **Both in corpus:** Q&A #6 (yearly) and PDF Section 1.5 (eLearning feature)
- **Issue:** Question ambiguous - "Schulungen" matches both frequency and feature
- **Client decision needed:** Which interpretation is preferred?

### Q14: VLM Data Loss (Official Contact)
- **Question:** "Mit welchen Kontaktdaten melde ich den externen Datenschutzbeauftragten?"
- **Expected:** email@iitr.de, Marienplatz 2, 80331 München, Tel (089) 18 91 73 60
- **RAG answered:** dskit@iitr.de (support email - wrong)
- **Root cause:**
  - Standard Docling chunks (95) contain official contact in chunk 3
  - VLM chunks (56) do NOT contain this information
  - Production uses VLM chunks → data lost
- **Fix options:**
  1. Use standard Docling chunks instead of VLM
  2. Add explicit Q&A pair for official DSB contact
  3. Merge both chunk sets

### Q21: Q&A Data Error (Wrong Chapter)
- **Question:** "Welche Dokumente brauchen wir unterschrieben von den Mitarbeitern?"
- **Expected:** Kapitel 11, Unterpunkt 2 - Verpflichtungserklärung
- **RAG answered:** Kapitel 9
- **Root cause:**
  - Q&A #9 says "Datenschutzverpflichtung" → Kapitel 09
  - PDF shows: Kapitel 09 = DSFA (impact assessment), Kapitel 11 = Employee forms
  - Q&A answer appears to be incorrect
- **Client confirmation needed:** Which chapter is correct for employee commitment forms?

---

## 6. Recommendations

### Content Additions (9 items)
Client should provide answers for:
1. DSB listing policy on customer websites (Q6)
2. Who files DSB registration - customer or IITR? (Q12)
3. Official DSB registration contact details (Q13)
4. eLearning tier pricing - 4 standard modules, 300€ for full access (Q16)
5. English certificate availability (Q17)
6. Login URL - core.iitr.de (Q18)
7. Post-employment email access agreement location (Q24)
8. Applicant data protection acknowledgment policy (Q27)
9. Website review service description and pricing (Q28)

### Q&A Correction (1 item)
- Row 9: Verify "Datenschutzverpflichtung" chapter reference (currently says Kapitel 09, likely should be Kapitel 11)

### Index Decision
Current production uses VLM chunks (70 docs) which lost official contact metadata.

Options:
1. **Revert to standard Docling** - Has official contact but more OCR noise
2. **Add targeted Q&A** - Supplement VLM with explicit Q&A for lost content
3. **Merge both** - Use VLM for content, add standard Docling header chunk

---

*Document created for client discussion. Add findings and decisions as comments below.*
