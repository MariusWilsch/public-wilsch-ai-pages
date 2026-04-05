---
publish: true
---

# Everyman 1 Polyphasic Sleep — Schedule Decision Framework
[[marius-sleep-schedule]]

Design doc supporting Marius's decision between E1 polyphasic sleep and alternative schedule optimizations. Uses Marius as an applied case study to teach readers how to derive their own sleep schedule from circadian science, evidence strength, and personal constraints.

---

## Problem Statement

Marius (23M, currently on a 12am–7am monophasic schedule ~7h) is exploring whether to adopt Everyman 1 polyphasic sleep (6h core + 20-min nap = ~6.3h total) or an alternative schedule optimization. The exploration is **pre-decision** — no experiment has started. He created the issue citing "experimental motivation" but without articulated goals, chronotype data, or validated baseline. The JA author (David) reports that both Marius and he experience "inconsistent schedule chaos" and occasional under-sleeping (5-6h on bad days).

Peer-reviewed evidence does not support strict polyphasic sleep as a performance strategy. The National Sleep Foundation's 2021 consensus panel ("Adverse impact of polyphasic sleep patterns in humans") documented adverse physical, mental, and performance outcomes and explicitly recommended against schedules that reduce total sleep or fragment it into multiple episodes. Evidence IS supportive of **biphasic/siesta** patterns (excluded from the NSF analysis) and of **monophasic consolidation** (the approach Rian Doris — the source Marius pointed to — actually advocates via "exhaustion ceiling" + "sleep as non-compressible Tetris block").

Marius therefore faces a schedule choice across three viable paths, with asymmetric evidence strengths and different demands on consistency, total sleep time, and adaptation cost. The decision cannot be made from the E1 polyphasic premise alone — it requires Marius to understand his own baseline, goals, and constraints against the evidence landscape.

**This design doc does NOT cover:** medical sleep disorder diagnosis, a prescriptive recommendation, the experiment protocol itself (that follows the schedule decision), or Flow State design-doc scope (tracked separately).

**Preconditions:**
- Subject is a young adult (23M) with no diagnosed sleep disorder
- Current schedule is a 7h monophasic core (12am–7am), not yet systematically optimized
- Subject has meaningful flexibility to modify sleep schedule (no documented shift-work constraint)
- No personal sleep tracking data exists — baseline is subjective self-report only
- Marius's chronotype, nap history, nap beliefs, and schedule constraints are undocumented — **Undefined**, flagged for meeting agenda
- JA author (David) writes for Marius as the subject — David is a skeptical reviewer who explicitly does not believe in naps, which does NOT determine Marius's stance
- Both subject and author share a stated "schedule chaos" pattern, which is material context regardless of which path is chosen

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A decision-support framework that lets Marius choose a sleep schedule path (monophasic-optimized, biphasic siesta, E1 polyphasic, or hybrid) with clear understanding of evidence strength, tradeoffs, risks, calibration method, and measurement approach — so the decision is informed rather than premise-driven. |
| **Success** | After reading the doc, Marius can articulate: (1) which path he's pursuing and why over the alternatives, (2) what his specific nap window is (if naps are in scope) given his chronotype, (3) what subjective/objective markers will signal the path is working, and (4) what warning signs will trigger abandonment. |
| **Done test** | Can we write a meeting agenda with open design questions that need Marius's direct input (chronotype, nap history, constraints, path preference)? → **YES, currently** → design is NOT complete, meeting needed → this pass delivers the framework; the decision follows Marius's input. |

---

## Approach

The decision framework decomposes into six parts that build on each other: Part 1 establishes the stakes (why schedule matters at all), Part 2 grounds the science (what determines optimal timing), Part 3 lays out the three viable paths with evidence, Part 4 provides the nap-window calibration method that any path may use, Part 5 applies the framework to Marius as a worked case study, and Part 6 defines how Marius will know whether the chosen path works.

### Part 1: Sleep as High-Performance State

Before choosing a schedule, Marius needs the frame that makes the choice matter. Rian Doris (MSc Neuroscience, King's College London; Flow Research Collective, 400K+ YouTube subs) — the source Marius pointed to — provides this frame. Rian is **not a polyphasic advocate**; he is a monophasic-consolidation advocate. His framing makes the stakes explicit, which is why it sits at the top of this doc regardless of which path Marius chooses.

**Sleep is the other side of flow.** Flow is an optimal state of consciousness where we perform at our best awake. Sleep is an optimal state of consciousness where the brain repairs, rebuilds, and regenerates to make peak performance possible. The two function like a seesaw: you build the neurochemistry of flow *while you sleep*. Sleep is not a gap in productivity — it is "an extreme form of productivity you're just not awake to observe."

**The exhaustion ceiling.** Suboptimal sleep creates a hidden limit to current performance. First-order consequences are obvious: brain fog, low energy, morning sluggishness. Second-order consequences are behavioral defaults that degrade — skipping an audiobook for a random YouTube video, reaching for caffeine by habit, shortening exercise. Third-order consequences show up as work quality collapse: inbox-clearing replaces deep work, irritability increases, relationships strain. The ceiling is hard to see because trickle-down effects are harder to track than single symptoms. People often attribute the downstream problems to other causes (bad colleagues, stressful job, lack of discipline) when the root cause is the ceiling itself.

**Sleep as non-compressible.** The most common scheduling mistake is treating sleep as the flex variable — the thing that shrinks when the day runs over. Work spills into evening → "only two hours to breathe" → sleep gets cut. This is the pattern that David reported ("sometimes 5-6h," "schedule chaos") and likely the pattern Marius shares. Rian's reframe: sleep is a **fixed, non-negotiable, non-compressible block** in a Tetris-shaped day. You cannot expand the container (24h). You fit tasks around the block, not the block around the tasks.

**Sleep is a trainable skill.** (Via Andrew Huberman, per Rian's account.) Sleep quality is not a matter of luck or genetic lottery. It is a skill — brain-wave composition (REM, deep non-REM, light non-REM) responds to trainable behaviors. Target composition for a healthy adult: 1-2 hours deep sleep, 2+ hours REM sleep. Insufficient REM directly compromises concentration, making REM indispensable for flow.

**Implication for schedule choice.** The three paths in Part 3 differ in how they treat the sleep block. Monophasic-optimized consolidates the block and protects it rigidly. Biphasic siesta splits the block into two biological commitments. E1 polyphasic compresses the block below recommended minimums while adding nap commitments. Each path has different demands on consistency, chronotype alignment, and adaptation tolerance. The "schedule chaos" baseline shared by David and Marius is a constraint against all three paths — any chosen path requires breaking the chaos pattern.

---

### Part 2: Circadian Mechanics & Individual Variation

Sleep timing isn't arbitrary. Two biological drives determine when sleep is easy and when alertness is high — and individual variation in these drives is large enough that population averages mislead at the person level.

**The two-process model.** Sleep propensity is driven by the interaction of two systems: a **homeostatic drive** (adenosine accumulates with waking hours — the longer you've been awake, the sleepier you get) and a **circadian drive** (a ~24h oscillation anchored to the suprachiasmatic nucleus). These combine to produce two peaks of sleep propensity across the 24-hour cycle: the **nocturnal peak** (high homeostatic + high circadian drive) and a smaller **afternoon peak** (circadian contribution still high, homeostatic moderate). Between them sits the **wake maintenance zone** — a late-evening alertness window around 8 PM where falling asleep is hardest, not easiest.

**The afternoon dip is circadian, not food-caused.** The "post-lunch dip" is a circadian phenomenon, not a digestive one. It occurs even when subjects skip lunch. The dip's timing is population-average around 1-4 PM but shifts substantially with individual chronotype. The same research literature that identified the dip also identified: "timing of the afternoon peak was significantly correlated with habitual wake time and melatonin rhythm" — meaning the dip is not at a fixed clock time, it is anchored to each person's biological day.

**DLMO and individual variation.** Dim-Light Melatonin Onset (DLMO) is the gold-standard marker of circadian phase — the moment when the pineal gland begins secreting melatonin as light fades. For young adults (ages 20-29, Marius's bracket), a large age-stratified study (n=1,749) measured DLMO ranging from **18:53 (6:53 PM) to 00:46 (12:46 AM)** — a ~4-hour variance within a single decade of life. This is not a clinical-population spread; it is healthy individual variation.

**Chronotype and the MEQ questionnaire.** Chronotype (morning-type vs evening-type) correlates with DLMO: morning types have earlier onsets, evening types have later onsets. The Morningness-Eveningness Questionnaire (MEQ) is the standard self-report instrument. It is statistically significant at the population level but **weak at the individual level**. Studies have documented that MEQ cannot reliably substitute for direct DLMO measurement — 85% of "delayed sleep-wake phase disorder" patients in reviewed studies fell within normal DLMO ranges, indicating MEQ-driven misdiagnosis frequency. Practical consequence: the MEQ can orient you (morning-type vs evening-type), but it cannot give you a precise DLMO time. For precision, you measure DLMO directly (saliva melatonin samples taken hourly in dim light across an evening).

**Why this matters for schedule choice.** Every schedule path in Part 3 asks Marius to position sleep relative to his internal biological day, not the wall clock. A "nap at 2 PM" recommendation assumes an average chronotype — useless if Marius is an evening type whose dip sits at 3:30 PM. Monophasic-optimized requires aligning core-sleep start with the beginning of the nocturnal propensity peak. Biphasic siesta requires aligning both the core and the siesta with biological troughs. E1 polyphasic compresses the core and demands nap placement at the afternoon dip — the tightest precision demand of the three. All three fail when they ignore individual variation. Part 4 provides a calibration method that derives schedule placement from habitual wake time, which is a usable proxy when direct DLMO measurement isn't available.

---

### Part 3: Three Schedule Paths

Marius has three viable paths with asymmetric evidence, demands, and risks. This part lays them out side-by-side so the choice is explicit. Each path below includes: what it is, evidence strength, demands, and specific warning signs.

#### Path A: Monophasic-Optimized (Rian Doris approach)

**What it is:** A consolidated 7-9 hour nighttime core, treated as non-compressible. The day is structured around the sleep block (Tetris model), not the other way around. Sleep hygiene protocols govern environment and behavior: room temperature ~18°C (65°F), last meal ≥3h before bedtime, caffeine cutoff ≥8h before bedtime, screen/light reduction 60-90 min pre-sleep, consistent bedtime/wake-time (±30 min) seven days per week.

**Evidence:** Strongest of the three paths. National Sleep Foundation and American Academy of Sleep Medicine joint consensus: adults need 7-9 hours regularly. Whitehall II study (n=5,431) shows 6-7h is within the optimal band and ≤5h or ≥9h associates with cognitive decline equivalent to a 4-7 year age increase. Rian Doris's protocols are grounded in mainstream sleep neuroscience (Walker, Huberman).

**Demands:** Strict bedtime/wake-time consistency (including weekends). A "sleep-first mindset" that resists compressing sleep when work or social demands spill into evening. Substantial behavioral restructuring if the current baseline is chaos: fixed bedtime requires fixed wind-down, fixed wind-down requires fixed work endpoint, fixed work endpoint requires scoping work against available hours. This cascade is the Tetris model in practice.

**Warning signs of failure:** Can't hold bedtime within ±60 min for 7 consecutive days. Weekend drift (compensatory sleep) exceeding 1h. Chronic need to catch up on sleep. These indicate the schedule is still being treated as compressible.

#### Path B: Biphasic Siesta

**What it is:** A shortened nighttime core (~6-7h) plus a substantial afternoon siesta (~60-90 min) aligned to the afternoon circadian dip. Total sleep remains at 7-8h — this path does NOT compress total sleep; it redistributes it. Examples: Mediterranean and Latin American cultures with sustained siesta traditions. The structure differs from polyphasic in that the siesta is long enough to complete one full sleep cycle (~90 min) rather than a short power nap.

**Evidence:** Supportive. The NSF 2021 consensus panel explicitly **excluded** siesta/biphasic schedules from their adverse-outcomes analysis — they found the evidence landscape for biphasic materially different from polyphasic. Cross-cultural longevity data from siesta-practicing populations is positive. One medical-student study cited in the polyphasic review: biphasic sleepers were the **most likely** to pass midterm exams (polyphasic were the least likely, monophasic in between).

**Demands:** Two biological commitments (core + siesta), both requiring consistent timing. Social compatibility — a 90-min afternoon sleep is incompatible with most 9-5 office schedules, though feasible for knowledge workers with schedule control. The siesta must be placed at the individual's afternoon dip (chronotype-dependent, typically 6-8 hours after wake time), not at a fixed clock time.

**Warning signs of failure:** Siesta consistently exceeds 90 min and creates sleep inertia spilling into the evening. Siesta shifts the nocturnal bedtime later (sleep-pressure insufficient at core-start). Inability to protect the siesta window against social/work encroachment.

#### Path C: E1 Polyphasic (Everyman 1)

**What it is:** A compressed nighttime core (~6 hours) plus one short afternoon nap (~20 min), total sleep ~6.3h. This is **below** the 7-9h consensus recommendation for young adults. The claim is that the 20-min nap plus compressed core delivers equivalent or superior alertness to full monophasic 7-8h — a claim that is not supported by peer-reviewed evidence at the cohort level.

**Evidence:** Weakest and most negative of the three. NSF 2021 Consensus Panel: "Striving to adopt a schedule that significantly reduces the amount of sleep per 24 hours and/or fragments sleep into multiple episodes throughout the 24-hour day is not recommended." Documented adverse outcomes: physical health, mental health, cognitive performance. Medical students on polyphasic were the **least likely** to pass midterm exams. The 7-14 day "adaptation period" is characterized in the literature as habituation to chronic sleep deprivation, not true physiological adjustment — subjective alertness habituates, but objective cognitive markers do not fully recover.

**Demands:** The tightest of the three. Rigid nap timing (±15 min) aligned to the afternoon dip. Strict core-start time aligned to the nocturnal propensity peak. 7-14 day crash period (severe fatigue, headaches, brain fog, motor coordination loss) before any habituation stabilizes. Inability to miss a scheduled nap without significant cognitive cost. Schedule fragility: a single missed nap or late core-start can collapse the adaptation.

**Warning signs of failure:** Multiple missed naps per week. Persistent brain fog past the 14-day crash period. Subjective alertness claim without objective confirmation (e.g., reading speed drops, decision quality drops). Reliance on caffeine to sustain the schedule (caffeine papers over the debt rather than repaying it). Social withdrawal due to schedule rigidity.

#### Path comparison at-a-glance

| Dimension | Path A: Monophasic-Optimized | Path B: Biphasic Siesta | Path C: E1 Polyphasic |
|-----------|------------------------------|-------------------------|----------------------|
| Total sleep | 7-9h | 7-8h (redistributed) | ~6.3h (compressed) |
| Evidence strength | Strongest | Supportive | Weakest, negative (NSF 2021) |
| Consistency demand | High (±30 min) | High × 2 (core + siesta) | Highest (±15 min, dip-aligned) |
| Adaptation cost | Low (behavioral) | Moderate (two blocks) | High (7-14 day crash) |
| Social compatibility | High (most schedules fit) | Low (90-min siesta) | Very low (rigid nap window) |
| Chronotype precision | Moderate | High | Highest |
| Abandonment cost | Low | Moderate | High (wasted adaptation) |

---

### Part 4: Nap Window Calibration Method

Paths B (biphasic siesta) and Path C (E1 polyphasic) both require placing a nap at the afternoon circadian dip. This part gives a reusable calibration method: how to derive YOUR nap window from your habitual wake time, without requiring direct DLMO measurement. Path A (monophasic-optimized) does not use this part directly but benefits from the same timing principles for its caffeine cutoff.

**Step 1: Establish habitual wake time.** Observe your actual wake time (not your target wake time) across 14 consecutive days including weekends. "Habitual" means what you do when you stop fighting it. If your weekday wake is 7 AM and weekend wake is 10 AM, your habitual wake is closer to 8-8:30 AM. This is the anchor for the calibration.

**Step 2: Estimate the afternoon dip window.** The afternoon circadian dip occurs approximately **6-8 hours after habitual wake time** for most people. If habitual wake is 7 AM, dip window is 1-3 PM. If habitual wake is 8:30 AM, dip window is 2:30-4:30 PM. This derivation is consistent with research findings that "timing of the afternoon peak was significantly correlated with habitual wake time."

**Step 3: Narrow to the dip midpoint.** Target the midpoint of the estimated window as the nap start time. For the 1-3 PM window, target 2 PM. For the 2:30-4:30 PM window, target 3:30 PM. This is a starting point, not a final answer — use it for 7 days, then adjust based on the sleep-latency test in Step 5.

**Step 4: Choose nap duration based on path.**

| Nap duration | Use case | Mechanism |
|--------------|----------|-----------|
| **<20 minutes** (power nap) | Path C (E1 polyphasic) | Stays in light sleep stages. Avoids slow-wave sleep. Minimal sleep inertia on waking. |
| **~90 minutes** (cycle nap) | Path B (biphasic siesta) | Completes one full sleep cycle (REM cycle). Waking at cycle end minimizes inertia. |
| **30-60 minutes** | **AVOID** | Danger zone. Highest probability of waking from slow-wave sleep → maximum sleep inertia, cognitive deficit up to 60 min post-wake. |

Note: The "90-minute cycle" is a population average. Actual individual cycle length varies widely — a large-scale polysomnography study (n=573, 16,441 nights) measured median cycle length at 110 minutes with 75% falling between 95-130 min. If 90 min consistently leaves you groggy, try extending to 100-110 min.

**Step 5: Test and refine using sleep latency.** After 7 days at the target nap time, measure average sleep onset latency (time to fall asleep). If latency is <5 minutes, the window is well-aligned to the dip. If latency is >15 minutes, the window is probably too early or too late — shift by 30 min and repeat. If you can't fall asleep at all, the window is significantly off or sleep pressure is insufficient (core sleep may be too long, caffeine too recent, or the dip itself may be atypical).

**Optional: Nappuccino protocol.** Pre-nap caffeine (~200mg, ≈2 cups brewed coffee) consumed immediately before the nap. Mechanism: caffeine has a 20-minute onset, matching the power-nap duration. The nap clears adenosine from the brain; caffeine arrives as the nap ends and blocks adenosine receptors from re-binding, producing a synergistic alertness boost. Pilot research (2020 Chronobiology International) documented improved vigilant attention and subjective alertness versus placebo in shift workers. Caveats: total daily caffeine matters (200mg + morning coffee may exceed individual tolerance), caffeine half-life is ~5-6 hours (a 2 PM nappuccino still has ~100mg in your system at 8 PM, potentially compromising nocturnal sleep quality).

**Optional: Light exposure countermeasure.** Bright light (2,000+ lux) for 1-2 minutes immediately after waking can improve *subjective* alertness. Research shows it does **not** reliably improve *objective* cognitive performance. Treat light as a secondary adjunct — useful but not a replacement for caffeine as an inertia countermeasure.

---

### Part 5: Marius Case Study

This part applies the framework to Marius's specific situation. It captures what is **known** (from the issue body and JA author input) and what is **Undefined** (requires Marius's direct input). The Undefined markers link to discussion topics in the meeting agenda.

**What is known:**

| Data | Value |
|------|-------|
| Age / demographic | 23M |
| Current schedule | 12am–7am core (7h monophasic) |
| Current total sleep (on-schedule days) | ~7h |
| Under-sleep frequency (from JA author inference) | "Sometimes 5-6h" due to schedule chaos |
| Documented source | Rian Doris (Flow Research Collective) — monophasic-consolidation advocate |
| Sleep disorder diagnosis | None documented |
| Personal tracking data | None |

**Inferences (low confidence, flagged for Marius validation):**

- **Chronotype:** A midnight bedtime with 7 AM wake suggests **slightly evening-leaning** chronotype. Evening-type patterns in the 20-29 DLMO range correspond to DLMO around 21:30–23:00. This is an inference from bedtime alone — NOT a measurement. **Undefined:** Marius's actual chronotype (MEQ score or DLMO measurement) is required to confirm — see Meeting Agenda Topic 1.
- **Afternoon dip window (using Part 4 calibration):** With 7 AM habitual wake, the 6-8h post-wake rule places the dip at **1-3 PM**. If chronotype is evening-leaning, the dip may shift 30-60 min later (1:30-3:30 PM). Target nap start: 2 PM (average chronotype) or 2:30 PM (evening-leaning inference). **Undefined until chronotype confirmed.**

**Risk assessment per path for Marius:**

| Path | Fit with Marius's baseline | Key risks |
|------|---------------------------|-----------|
| **Path A: Monophasic-optimized** | **Strong fit as starting point.** Matches Rian Doris's actual recommendations (the source Marius pointed at). Addresses the documented "schedule chaos" pattern directly. Preserves 7h baseline. Low adaptation cost. | Requires behavioral discipline Marius has not yet demonstrated (per JA author's "schedule chaos" report). If discipline fails, this path reveals itself as insufficient within 2-4 weeks. |
| **Path B: Biphasic siesta** | **Moderate fit.** Positive evidence base, aligned with some cultural practice, allows schedule variety. But: requires 90-min afternoon block inconsistent with many work schedules. | Social/work incompatibility if Marius has meetings in the 2-4 PM window. Siesta drift (oversleeping into inertia) is common in early weeks. |
| **Path C: E1 polyphasic** | **Poor fit given baseline.** Total sleep REDUCES from current 7h to ~6.3h, below consensus recommendation for young adults. Adds consistency demand (±15 min nap timing) to a reporter with schedule-chaos pattern. NSF 2021 explicitly advises against this path. | 7-14 day adaptation crash with real cognitive/work cost. Risk of treating habituation-to-deprivation as successful "adaptation." High abandonment cost. Contradiction: Rian Doris (Marius's source) does NOT advocate this path. |

**Undefined items (all require Marius's direct input — see Meeting Agenda):**

- **Undefined — Nap stance and history:** Does Marius currently nap? Has he ever adopted a consistent nap practice? Does he experientially believe in naps? JA author does not know Marius's stance. Without this, path selection between B/C (nap-involved) and A (no-nap) is premature. → Meeting Agenda Topic 2.
- **Undefined — Chronotype:** No MEQ score or DLMO measurement on record. Affects Path B/C nap-window precision and Path A sleep-hygiene caffeine cutoff calculation. → Meeting Agenda Topic 1.
- **Undefined — Schedule constraints:** Work meetings, client calls, fixed commitments, social patterns. These gate what paths are even feasible (e.g., a 90-min siesta is incompatible with dense afternoon meetings). → Meeting Agenda Topic 3.
- **Undefined — Prior monophasic optimization:** Has Marius previously attempted to systematically optimize his 7h monophasic (sleep hygiene, bedtime discipline, caffeine timing) before considering polyphasic? If yes, what failed. If no, Path A is arguably a prerequisite to consider before Path C. → Meeting Agenda Topic 4.
- **Undefined — Motivation for Rian Doris link:** Marius sent the Rian Doris link, but Rian advocates monophasic-consolidation, not polyphasic. Did Marius know that? Is he signaling openness to Path A specifically, or using Rian as a general performance/flow reference? → Meeting Agenda Topic 5.
- **Undefined — Success criteria weighting:** Marius's stated success spans all four outcomes (time gain, flow state, feeling rested, validated experiment). When these conflict (e.g., Path C reduces time awake but compresses total sleep), which dominates for Marius? → Meeting Agenda Topic 6.

---

### Part 6: Measurement & N=1 Experimentation

Whichever path Marius chooses, he needs a measurement protocol that distinguishes "this is working" from "I've habituated to feeling bad." This part provides a minimal N=1 experiment design that does not require specialized hardware.

**The subjective-objective disconnect.** Research on self-reported sleep consistently finds that subjective reports inflate sleep duration by **~0.8 hours on average** compared to objective measurement. Subjective reports also habituate to chronic mild deprivation — people stop noticing they're tired. This is why a single self-report question ("do you feel rested?") is insufficient as the success metric. The protocol below combines subjective, semi-objective, and behavioral markers to cross-validate.

**Minimum marker set (no hardware required):**

| Marker type | What to record | When |
|-------------|----------------|------|
| **Subjective — morning alertness** | 1-10 scale on waking ("how clear is my head right now?") | Within 5 min of waking, before caffeine |
| **Subjective — afternoon energy** | 1-10 scale ("how alert am I, independent of caffeine?") | 2 PM daily |
| **Semi-objective — sleep latency** | Minutes to fall asleep at core-start (estimated) | Next morning, retrospective |
| **Semi-objective — wake episodes** | Count of remembered mid-sleep wakings | Next morning |
| **Behavioral — work output quality** | Did I complete deep work or default to inbox-clearing? | End of workday, yes/no |
| **Behavioral — caffeine intake** | Total mg for the day | End of day |

These markers take <3 minutes per day to log. Minimum protocol: 14 consecutive days at current baseline, then 28 consecutive days on the chosen path. A sleep diary (paper, Notes app, or structured template) is sufficient.

**Optional: Hardware-supported markers.** If Marius has or is willing to acquire an Oura ring, Apple Watch with sleep tracking, or similar wearable: add HRV, resting heart rate, REM duration, deep-sleep duration. Autoethnography with Oura-ring tracking is a validated N=1 methodology (published in sleep-research literature). Wearables do not replace the subjective markers above — they add convergent evidence.

**Experiment structure (2-phase minimum):**

1. **Phase 0 — Baseline (14 days):** Hold current 12am-7am monophasic schedule. Log markers daily. Goal: establish ground truth of current state. Without this, claims of improvement are unanchored.
2. **Phase 1 — Chosen path (28 days):** Execute the chosen path with strict consistency. Log markers daily. Do NOT evaluate the experiment before day 28 — the first 7-14 days are adaptation/disruption, not steady-state.

Compare Phase 1 average against Phase 0 average on each marker. Improvement is a ≥1-point shift on subjective scales AND ≥1 behavioral improvement (e.g., more deep-work days per week).

**Warning signs that trigger path abandonment:**

- Morning alertness score drops ≥2 points from baseline and doesn't recover by day 21
- Behavioral marker (deep-work completion) degrades week-over-week through day 28
- Caffeine intake climbs to sustain the schedule (compensating rather than adapting)
- Path C specific: persistent brain fog past day 14 is the signal that habituation is masking deprivation, not that adaptation is pending
- Any path: consistency compliance drops below 5/7 days per week — the path isn't being tested, just sampled

**Decision after Phase 1:**
- **Markers improved:** Continue path, extend to 90 days, retest
- **Markers neutral:** Try Path A minimum baseline (if not already tried) before further experimentation
- **Markers worsened:** Return to Phase 0 baseline, revisit path choice in design doc

---

## Source

- **Issue:** [DaveX2001/deliverable-tracking#1343](https://github.com/DaveX2001/deliverable-tracking/issues/1343)
- **Session (extraction pass):** `/Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/a856d3fd-53f8-4ff6-bc89-e712f89b2869.jsonl`
- **Author stance:** JA author (David) reported schedule chaos baseline + explicit non-belief in naps. This is reviewer context, NOT subject context — Marius's stance requires separate confirmation (see Meeting Agenda).

**Research sources (full-text read):**
- NSF 2021 Consensus Panel — *Adverse impact of polyphasic sleep patterns in humans* — [PubMed: 33795195](https://pubmed.ncbi.nlm.nih.gov/33795195/)
- Whitehall II Study — *Change in Sleep Duration and Cognitive Function* — [PMC3079935](https://pmc.ncbi.nlm.nih.gov/articles/PMC3079935/)
- DLMO Age-Stratified Study (n=1,749) — *The dim light melatonin onset across ages, methodologies, and sex* — [Oxford SLEEP 2023](https://academic.oup.com/sleep/article/46/5/zsad033/7044190)

**Research sources (summary-level via domain web search):**
- Two-process model & sleep propensity — [PMC2647793](https://pmc.ncbi.nlm.nih.gov/articles/PMC2647793/)
- Caffeine-nap pilot (2020 Chronobiology International) — [PubMed: 32819191](https://pubmed.ncbi.nlm.nih.gov/32819191/)
- Ultradian cycle variation — [Sleep Health Journal](https://www.sleephealthjournal.org/article/S2352-7218(23)00204-8/fulltext)
- Sleep inertia review — [PMC5136610](https://pmc.ncbi.nlm.nih.gov/articles/PMC5136610/)
- NSF/AASM 7-9h consensus — [PMC4434546](https://pmc.ncbi.nlm.nih.gov/articles/PMC4434546/)

**Video source (transcribed):**
- Rian Doris — *Sleep as Foundation of Flow* — [YouTube: AWoN9mqMF2E](https://www.youtube.com/watch?v=AWoN9mqMF2E) (~10 min transcribed via yt-dlp + AssemblyAI, covers exhaustion ceiling, Tetris model, sleep-as-skill framing)

**Meeting agenda:** [E1 Polyphasic Sleep — Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/e1-polyphasic-sleep-meeting-agenda)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

