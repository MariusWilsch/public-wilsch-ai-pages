---
publish: true
---

# REKERS Evaluierungsliste — Validierungsdesign Besprechung
[[client-rekers]]

## Meeting Goal

Resolve the validation design for the V006 feasibility report so that POC testing can proceed with a shared definition of "correct results."

1. **Evaluierungsliste defined** — REKERS provides test queries with expected matches in a CSV template we supply
2. **Red herring approach agreed** — consensus on how to test "nothing found" scenarios
3. **Ground truth ownership clear** — who fills what, and what "similar" means for each test case

## Pre-Read

- [Design Doc — Feasibility Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report) (Part 4: Validation Design)
- [Data Assessment — Testdaten](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. We have 14 delivered projects — selecting test queries from them

We received 14 project folders with Anfragen + Angebote data. For validation, we need specific Anfragen that serve as test inputs — queries where REKERS already knows which historical projects should come back as "similar." The leave-one-out approach uses 9 projects as reference, queries the 10th, and checks if the expected matches appear.

We have a 4-column CSV template ready: `Test_Anfrage_ID`, `Expected_Match_1`, `Red_Herring`, `Match_Reason`. The Match_Reason captures *why* two projects are similar — which of the 9 criteria (Gebäudetyp, Höhe, Kran, Dachlasten, Kundenreferenz, Kalkulatorenwissen, Baustoff, Dachbegrünung, Bauort) drive the similarity.

### 2. The system must be able to say "nothing found"

From Workshop 2: *"Ist schlecht programmiert, wenn die KI nicht sagen kann, tut mir leid, nichts gefunden."* We need test cases where no match is the correct answer — a Bürogebäude query against a reference set of Hallen, or a new customer type that has no historical precedent. These "red herring" test cases validate that the system doesn't force irrelevant results.

How REKERS defines these cases — and whether they come from existing data or are constructed scenarios — is still an open point (TOUCHED, not fully defined from our side).

### 3. Similarity means different things depending on what you're calculating

Workshop 2 established that criteria weighting is context-dependent: *"Wenn ich kalkuliere, ist die Dachbegrünung maßgeblich, wenn ich die Stütze kalkuliere, ist ja Höhe maßgeblich."* The Evaluierungsliste captures which criteria drive similarity for each test case (via `Match_Reason`), but it would help to understand — for the specific test queries chosen — what makes them "similar" in the Kalkulator's daily work.

### 4. Most matching criteria live in documents, not in the database

Our data analysis found that 3 of 9 criteria are in the Anfragen CSV (Bauort, Kundenreferenz, Kalkulatorenwissen) while 6 require extraction from project files (Gebäudetyp, Höhe, Kran, Dachlasten, Baustoff, Dachbegrünung). Initial verification across 3 projects showed 5/6 extractable from PDFs and emails. For the Evaluierungsliste, this means some "expected matches" may be based on criteria the AI can only discover through document analysis — which is part of what we're testing.

## Meeting Format

- **Type:** Working session (Herr Sasse + Marius, optionally Kalkulator)
- **Expectation:** Herr Sasse comes with familiarity of the 14 delivered projects and a sense of which are "similar" to each other
- **Outcome:** Filled Evaluierungsliste CSV (or clear assignment of who fills it by when)

## Related

- **Issue:** [#629 — REKERS: KI-gestütztes Angebotssystem - Workshop 2](https://github.com/DaveX2001/deliverable-tracking/issues/629)
- **Design Doc:** [Feasibility Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Data Assessment:** [Testdaten Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)
