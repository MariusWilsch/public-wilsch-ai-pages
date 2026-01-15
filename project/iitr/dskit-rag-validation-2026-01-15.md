---
publish: true
---

# DS-Kit RAG Validation Report - 2026-01-15

Validation of test failures against source documents (PDF + CSV) for client meeting.

---

## Summary

| Metric | Value |
|--------|-------|
| Total Questions | 29 |
| Working | 17 (58.6%) |
| Failures Validated | 12 |

### Failure Categories

| Category | Count | Color | Action Required |
|----------|-------|-------|-----------------|
| TRUE DATA GAP | 9 | Red | Client must provide content |
| VLM DATA LOSS | 1 | Blue | Fixable - add header chunk |
| Q&A DATA ERROR | 1 | Green | Fixable - correct CSV |
| AMBIGUOUS | 1 | Yellow | Client decides preference |

---

## Q5: Schulungen / eLearning

**Category:** AMBIGUOUS

### Question
Kriegen wir auch Schulungen, damit wir unsere Mitarbeiter ab und zu mal beim Datenschutz auf den Stand bringen?

### Expected Answer
Über das Datenschutz-Kit können Sie im Bereich "eLearning" auf eine Auswahl von Schulungsmodulen zugreifen, mithilfe derer Sie Ihre Beschäftigten unter anderem im Bereich Datenschutz unterweisen können. Unter "Statistik" finden Sie die Dokumentation der absolvierten und nicht absolvierten Schulungen.

### RAG Response
Ja, Sie erhalten Schulungen, um Ihre Mitarbeiter regelmäßig beim Thema Datenschutz zu schulen. Die Datenschutzschulung der Mitarbeiter sollte mindestens einmal im Jahr erfolgen. Dies ergibt sich aus der Erwartung der deutschen Datenschutz-Aufsichtsbehörden.

### Source Check: In Data?
- **PDF p.9-10:** eLearning features exist ✓
- **CSV Row 6:** Frequency answer exists ✓
- **Result:** BOTH answers exist in source data

### Source Check: Retrieved by RAG?
Retrieved 6 chunks (Typesense search "Schulung"):
1. **Rank 1 (CSV):** "Wie häufig sollte ich... schulen? - mindestens einmal im Jahr" ← LLM used this
2. **Rank 2 (PDF):** Statistik feature description
3. **Rank 3 (PDF):** eLEARNING overview
4. **Rank 4 (PDF):** Schulungsmodul selection
5. **Rank 5 (CSV):** Beratungsstunde (not relevant)
6. **Rank 6 (PDF):** Mitarbeiterschulung in eLEARNING

**Result:** eLearning content WAS retrieved (ranks 2-6), but LLM chose rank 1 (frequency)

### Client Discussion
Client decides: Feature description (where/how to access) OR frequency (how often)?

---

## Q6: DSB auf Webseite angeben

**Category:** TRUE DATA GAP

### Question
Kann ich Sie als Datenschutzbeauftragter auf meiner Webseite angeben?

### Expected Answer
Wenn Sie uns als Ihren externen Datenschutzbeauftragten bestellt haben, können Sie uns auf Ihrer Webseite sowie in Verträgen und bei der zuständigen Datenschutzaufsichtsbehörde mit den folgenden Kontaktdaten benennen: IITR Datenschutz GmbH, Dr. Sebastian Kraska, Marienplatz 2, 80331 München, E-Mail: email@iitr.de, Telefon: 089 189 173 60

### RAG Response
Nein, Sie können uns nicht als Datenschutzbeauftragten auf Ihrer Webseite angeben. Der Datenschutzbeauftragte des DS-Kit ist die IITR Datenschutz GmbH, vertreten durch Dr. Sebastian Kraska.

### Source Check
- **PDF:** NOT FOUND (16 pages checked)
- **CSV:** NOT FOUND (14 rows checked)
- **Result:** Policy about listing IITR on customer website does NOT exist

### Client Discussion
Client must provide: Policy statement that customers CAN list IITR as DSB on their websites + contact details to use.

---

## Q12: DSB Meldung - Wer?

**Category:** TRUE DATA GAP

### Question
Übernehmen Sie die Meldung des externen Datenschutzbeauftragten?

### Expected Answer
Die Meldung des Datenschutzbeauftragten bei der zuständigen Datenschutzaufsichtsbehörde muss durch den Verantwortlichen erfolgen. Wenn Sie uns als Ihren externen Datenschutzbeauftragten bestellt haben, sollten Sie die Meldung bei Ihrer zuständigen Datenschutzbehörde bitte selbstständig vornehmen. Weitere Informationen hierzu finden Sie unter Kapitel 1 des Datenschutz-Kits.

### RAG Response
Die Meldung des externen Datenschutzbeauftragten (DSB) an die zuständige Datenschutz-Aufsichtsbehörde erfolgt gemäß den Vorgaben des DS-Kit. Dazu steht Ihnen das Dokument 01-02 DS-Kit Meldung DSB an Aufsicht zur Verfügung.

### Source Check
- **PDF p.11:** Document 01-02 exists for filing ✓
- **BUT:** Does NOT say WHO must file
- **CSV:** NOT FOUND
- **Result:** Clarification that CUSTOMER files (not IITR) is missing

### Client Discussion
Client must clarify: CUSTOMER (Verantwortlicher) files DSB registration, not IITR.

---

## Q13: DSB Meldung - Wo?

**Category:** TRUE DATA GAP

### Question
Wo melde ich den externen Datenschutzbeauftragten?

### Expected Answer
Wenn Sie uns als Ihren externen Datenschutzbeauftragten bestellt haben, können Sie uns auf Ihrer Webseite sowie in Verträgen und bei der zuständigen Datenschutzaufsichtsbehörde mit den folgenden Kontaktdaten benennen: IITR Datenschutz GmbH, Dr. Sebastian Kraska, Marienplatz 2, 80331 München, E-Mail: email@iitr.de, Telefon: 089 189 173 60

### RAG Response
Die Frage, wo Sie den externen Datenschutzbeauftragten melden, wird im Kontext nicht direkt beantwortet.

### Source Check
- **PDF p.1:** Contact in header exists ✓
- **BUT:** Not in DSB registration context
- **CSV:** NOT FOUND
- **Result:** DSB registration contact details missing in proper context

### Client Discussion
Client must provide: DSB registration contact details in proper context.

---

## Q14: DSB Kontaktdaten

**Category:** VLM DATA LOSS

### Question
Mit welchen Kontaktdaten melde ich den externen Datenschutzbeauftragten?

### Expected Answer
IITR Datenschutz GmbH, Dr. Sebastian Kraska, Marienplatz 2, 80331 München, E-Mail: email@iitr.de, Telefon: 089 189 173 60

### RAG Response
Für die Kontaktaufnahme: IITR Datenschutz GmbH, Dr. Sebastian Kraska, E-Mail: dskit@iitr.de

### Source Check
- **PDF p.1 HEADER:** email@iitr.de, Marienplatz 2, Tel (089) 18 91 73 60 EXISTS ✓
- **Result:** VLM parsing LOST header data (wrong email: dskit@ instead of email@)

### Client Discussion
**FIXABLE:** Add PDF header chunk to index, or use standard Docling instead of VLM.

---

## Q16: eLearning 300€ Pricing

**Category:** TRUE DATA GAP

### Question
Ich bin bereits Kunde des Datenschutz-Kits. Wie kann ich auf weitere eLearning Module zugreifen?

### Expected Answer
Im Rahmen des Datenschutz-Kit Basis stehen Ihnen standardmäßig vier eLearning-Schulungsmodule zur Verfügung. Zu einem Aufpreis von 300 € netto jährlich schalten wir Ihnen gerne den Vollzugriff auf alle Schulungsinhalte frei. Bitte wenden Sie sich hierzu an dskit@iitr.de

### RAG Response
Wenn Sie bereits Kunde des Datenschutz-Kits sind und auf weitere eLearning-Module zugreifen möchten, können Sie sich an den Support wenden. Bitte senden Sie eine E-Mail an dskit@iitr.de.

### Source Check
- **PDF p.16:** 11-03 DS-Kit eLearning mentioned, BUT no pricing
- **CSV:** NOT FOUND
- **Result:** "vier Module" and "300€" NOT in corpus

### Client Discussion
Client must provide: eLearning tier pricing (4 standard modules, 300€ for full access).

---

## Q17: Englisch Zertifikat

**Category:** TRUE DATA GAP

### Question
Stellen Sie auch englischsprachige Zertifikate zur Verfügung?

### Expected Answer
Wir können Ihnen gerne ein englischsprachiges Datenschutz-Zertifikat ausstellen, mit welchem Sie Ihre datenschutzrechtliche Befassung nachweisen können. Bitte wenden Sie sich hierzu an dskit@iitr.de

### RAG Response
Die DS-Kit-Plattform bietet standardmäßig keine englischsprachigen Zertifikate an. Sollten Sie dennoch einen englischsprachigen Nachweis benötigen, können Sie sich an das DS-Kit-Team wenden.

### Source Check
- **PDF:** NOT FOUND
- **CSV:** NOT FOUND
- **Result:** English certificate service NOT documented

### Client Discussion
Client must provide: English certificate availability (answer is YES, not NO).

---

## Q18: Login URL

**Category:** TRUE DATA GAP

### Question
Wie erhalte ich einen Zugang zum Datenschutz-Kit?

### Expected Answer
Bitte stellen Sie zunächst sicher, dass die IITR Datenschutz GmbH Ihnen einen Benutzerzugang zum Datenschutz-Kit eingerichtet hat. Sodann können Sie core.iitr.de aufrufen und unter Angabe Ihrer E-Mailadresse einen Login-Link anfordern.

### RAG Response
Um einen Zugang zum Datenschutz-Kit zu erhalten, wenden Sie sich bitte direkt an das IITR Datenschutz GmbH. Sie können uns eine E-Mail an dskit@iitr.de senden.

### Source Check
- **PDF:** NOT FOUND
- **CSV:** NOT FOUND
- **Result:** Login URL "core.iitr.de" NOT in corpus

### Client Discussion
Client must provide: Login URL (core.iitr.de).

---

## Q21: Mitarbeiter Dokumente

**Category:** Q&A DATA ERROR

### Question
Welche Dokumente brauchen wir unterschrieben von den Mitarbeitern?

### Expected Answer
Die Beschäftigten sollten die Verpflichtungserklärung auf Einhaltung des Datenschutzes unterzeichnet haben. Eine entsprechende Vorlage finden Sie unter Kapitel 11, Unterpunkt 2 in Ihrem Datenschutz-Kit.

### RAG Response
Für die Arbeit mit dem Datenschutz-Kit und die Erfüllung der datenschutzrechtlichen Pflichten gibt es mehrere Dokumente...

### Source Check
- **CSV Row 9:** Says "Kapitel 09"
- **Expected:** "Kapitel 11, Unterpunkt 2"
- **Result:** Q&A CSV has WRONG chapter reference

### Client Discussion
**FIXABLE:** Correct CSV Row 9 from "Kapitel 09" to "Kapitel 11, Unterpunkt 2".

---

## Q24: E-Mail Vereinbarung Video

**Category:** TRUE DATA GAP

### Question
In welchen Videos wird die Personal-Vereinbarung bzgl. E-Mail nach Beendigung besprochen?

### Expected Answer
Eine Vorlage für eine Vereinbarung über den Zugriff auf die E-Mail-Adresse des Beschäftigten nach Beendigung des Beschäftigungsverhältnisses finden Sie in Kapitel 11, Unterpunkt 3 Ihres Datenschutz-Kits.

### RAG Response
Die Information über E-Mails mit privatem Inhalt wird nicht direkt in Verbindung mit Videos im Portal erwähnt.

### Source Check
- **PDF:** NOT FOUND
- **CSV:** NOT FOUND
- **Result:** Post-employment email agreement location NOT in corpus

### Client Discussion
Client must provide: Where to find post-employment email agreement (Kapitel 11, Unterpunkt 3).

---

## Q27: Bewerber Unterschrift

**Category:** TRUE DATA GAP

### Question
Müssen unsere Bewerber unsere Datenschutzbestimmungen unterschreiben?

### Expected Answer
Es ist nicht erforderlich, dass Bewerber Datenschutzhinweise unterschreiben. Bewerber sollten jedenfalls die Datenschutzhinweise zur Kenntnis nehmen.

### RAG Response
Die Frage, ob Bewerber Ihre Datenschutzbestimmungen unterschreiben müssen, wird in den bereitgestellten Dokumenten nicht direkt beantwortet.

### Source Check
- **PDF:** NOT FOUND
- **CSV:** NOT FOUND
- **Result:** Applicant data protection policy NOT in corpus

### Client Discussion
Client must provide: Applicant policy (acknowledge, NOT sign).

---

## Q28: Cookie Banner

**Category:** TRUE DATA GAP

### Question
Ist unser Cookie Banner so in Ordnung?

### Expected Answer
Gerne prüfen wir Ihre Webseite auf Datenschutzkonformität zu unserem Stundensatz von 180€ netto (Abrechnung im 10-Minuten-Takt). Bitte wenden Sie sich dazu an dskit@iitr.de

### RAG Response
Die derzeit genutzte Cookie-Entscheidung mit den Optionen „ich stimme zu" und „ich stimme nicht zu" ist nicht vollständig datenschutzkonform...

### Source Check
- **PDF:** NOT FOUND
- **CSV:** NOT FOUND
- **Result:** Website review service (180€) NOT in corpus

### Client Discussion
Client must provide: Website review service description + pricing (180€).

---

## Action Items for Client

### Must Provide (9 items)
1. **Q6:** Policy for listing IITR as DSB on customer websites
2. **Q12:** Clarification that customer files DSB registration
3. **Q13:** DSB registration contact details
4. **Q16:** eLearning tier pricing (4 modules, 300€ full)
5. **Q17:** English certificate availability
6. **Q18:** Login URL (core.iitr.de)
7. **Q24:** Post-employment email agreement location
8. **Q27:** Applicant data protection policy
9. **Q28:** Website review service description

### Fixable by IITR (2 items)
1. **Q14:** Add PDF header chunk to recover contact details
2. **Q21:** Correct CSV Row 9 chapter reference

### Client Decision (1 item)
1. **Q5:** Feature description vs frequency - which is preferred answer?
