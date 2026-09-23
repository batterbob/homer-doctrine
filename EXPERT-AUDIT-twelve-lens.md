# Daily Brief + Hermes — Twelve-Lens Expert Audit (Final)

> **Note on this public edition.** This is stage two of three. Stage one
> (`CROSS-FUNCTIONAL-REVIEW-25-recommendations.md`) was an *unsourced* panel of
> expert personas proposing 25 features. This document audits that panel against
> the **published literature** and rejects a good deal of it. Stage three
> (`DESIGN-RATIONALE.md`) is the distilled set of what survived, with verified
> DOIs.
>
> Citations here are real — named researchers, journals, effect sizes — and the
> document is deliberately self-critical about weak evidence: it flags the widely
> repeated "23 minutes 15 seconds" interruption figure as coming from a 2006
> Gallup interview rather than the CHI papers it is usually attributed to, and
> marks the Gottman 5:1 ratio as a correlational marriage-lab finding to be read
> directionally rather than instrumented.
>
> Redaction for publication: the two adults are "the operator" and "the
> co-operator", the children are "the older/younger child", and one teacher's
> name was replaced with "the teacher". A specific child's age was generalized to
> "early adolescence". Nothing else was altered — no finding, citation, number or
> recommendation.
>
> *(The original title said "Ten-Iteration"; there are in fact twelve. Retitled
> here, otherwise unchanged.)*

## Executive Summary

**Verdict: This is one of the best-architected personal information systems I have reviewed against the behavioral-science literature. Its core invariants — “silence is earned,” noise budgeting, “every problem ships with its smallest next action,” and “wins are always surfaced” — are not folk wisdom; they are near-exact operationalizations of findings from alarm-fatigue research, implementation-intention theory, behavioral activation, and negativity-bias research. The system’s biggest risks are not in what it does today but in what it plans to add.**

The strategic goals score as follows:

|#|Goal                                                         |Status                           |One-line justification                                                                                                                                           |
|-|-------------------------------------------------------------|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
|1|Nothing falls through cracks, minimum attention, both parents|**Partially baked in**           |Deterministic detection + “silence is earned” guarantees catch; the *both-parents* half is weaker (the co-operator’s interface is thin, single-operator dependence is real)|
|2|Minimize chattiness/nagging                                  |**Fully baked in**               |4-ping cap, batching to fixed sends, silent-when-nothing channels, streak silence — all evidence-aligned                                                         |
|3|Every problem ships with a solution                          |**Fully baked in**               |The smallest-next-action invariant is the strongest single design decision in the system                                                                         |
|4|Clear, concise, sticky, effective, enjoyable                 |**Partially baked in**           |Clear/concise/effective yes; “sticky” and “enjoyable” rest on a motivational economy that is mostly right but has one latent trap (below)                        |
|5|Proactive, options-with-approval, never autonomous           |**Partially baked in (roadmap)** |The approve/edit/skip draft-handoff is textbook trust-calibrated automation, but it is planned, not deployed                                                     |
|6|Kids’ wellbeing/relationships are the end, not surveillance  |**Partially baked in / asserted**|The psych guardrails are excellent *as written*; but they are largely enforced by the operator’s judgment and prompt discipline, not by code — this is the softest goal |

The single most important finding: **the system is production-grade on noise discipline and problem-to-action framing, but it is dangerously dependent on one human (the operator), and its 25-recommendation growth backlog is the primary threat to the very minimalism that makes it work.** The correct posture for the coming year is *consolidate and harden, not expand.*

Below are the ten-plus iterations, then a consolidated recommendation list, a “what NOT to build” section, a one-page operating philosophy, and a bibliography.

-----

## Iteration 1 — Lead: Notification/Interruption Scientist

**Top-down (goal → design).** Goal 2 (minimize chattiness) and Goal 1 (nothing falls through) are in direct tension: the way to never miss something is to alert on everything, which destroys attention. Does the architecture resolve this? Largely yes. The system separates *detection* (deterministic, exhaustive, cheap) from *notification* (budgeted, capped, batched). This is the correct decomposition. Fitz, Kushlev, Jagannathan, Lewis, Paliwal & Ariely (2019, *Computers in Human Behavior*, 101:84–94), in a 237-person randomized field experiment, found that delivering notifications in **three predictable daily batches** led to participants who “felt more attentive, productive, in a better mood, and in greater control of their phones,” with lower stress — whereas batching *hourly* produced effects no better than the as-usual control, and turning notifications *off entirely* produced participants who “experienced higher levels of anxiety and ‘fear of missing out.’” The Daily Brief’s fixed sends (6:00 / afternoon / 20:30 close-of-day after consolidation / Sunday) are almost exactly the “few predictable batches” arm that won.

**Bottom-up (details → goal).** The 4-pings-per-day cap with a logged counter reviewed after the first September school week, and a cut line if any day exceeds 4, is the right instinct and roughly evidence-aligned — the winning arm of the Fitz/Kushlev study delivered notifications three times a day. There is no literature that blesses “4” as a magic number, so present it honestly as a reasoned default, not a proven threshold. The consolidation of the 20:00 submission check and 20:45 standup into a single 20:30 “close of day” is unambiguously correct: it removes a redundant switch. Gloria Mark’s work (Mark, Gonzalez & Harris, 2005, *CHI*, “No Task Left Behind?”; Mark et al., 2008, *CHI*, “The Cost of Interrupted Work”) established that interrupted knowledge workers take roughly 23 minutes to resume an interrupted task (the widely cited “23 minutes 15 seconds” figure comes from Mark’s 2006 Gallup interview reporting that 81.9% of interrupted work was resumed the same day; note the exact number appears in interviews rather than the CHI papers themselves, and a 2005 diary study reported closer to 25 minutes) and that context-*different* interruptions are the disruptive kind — so collapsing two evening pings into one is a real cognitive saving, not a cosmetic one.

**Key findings.** Fully baked in for Goal 2. The one risk: the “always sends at 6am” brief (Iteration 4) is defended as a heartbeat, which is defensible but should be audited against the batching evidence.

**Recommendations.**

- Keep the ≤4/day cap; **rename the log metric “context-switches induced,” not “pings,”** so a single message carrying five items counts as one switch (aligns the metric with what Mark’s research says actually costs attention).
- Make the September review a hard gate: **if any weekday exceeds 4 context-switches, the newest-added notification slot is auto-flagged for the cut**, per the “new features must replace slots, not add them” rule.

-----

## Iteration 2 — Lead: Physician / Clinical Alarm-Fatigue Specialist

**Top-down.** The clinical alarm-fatigue literature is the single most transferable body of evidence for this system, because a home-monitoring pipeline is structurally identical to a patient monitor: a stream of automated alerts to a human who can become desensitized. Research shows **80–99% of ECG monitor alarms are false or clinically insignificant** (AHRQ PSNet, Jacques & Williams 2016), and — as PSNet defines the mechanism — “alarm fatigue occurs when busy workers are exposed to numerous frequent safety alerts and as a result become desensitized to them… [leading] to longer response times or to missing important alarms.” That is the exact failure mode that would make the operator stop reading the brief, and it is precisely what the system’s own psych review anticipates (“vigilance systems become dreaded then avoided”). The design principle that follows: every non-actionable alert is a toxin. The system’s “silent when nothing due” channels (afternoon delta, 20:00/20:30 submission check) are the correct application — they refuse to cry wolf.

**Bottom-up.** “Silence is earned” — every silent-failure path must alert with a suggestion (fetch guards, volume checks, extraction flags, baseline flags) — is the *inverse* of alarm fatigue and is exactly right: it converts the dangerous case (system broke, no one knows) into a loud, actionable, rare event. The alarm literature’s cautionary tale is the low-positive-predictive-value alarm: high sensitivity with low PPV *is* alarm fatigue (a nuisance alarm the operator learns to ignore). The system’s deterministic detection plus the “budgeted noise” caps are the design answer — high sensitivity in detection, high specificity in notification.

**Key findings.** Fully baked in for Goals 2 and 3. The open verification-debt items (calendar-conflict noise, school-notes quality, the cafeteria portal detector) are the PPV risk: an unvalidated parser that fires on noise will erode trust faster than a missed item.

**Recommendations.**

- Treat every verification-debt parser as **“quarantined until PPV observed”**: on first live school-year data, log fire/true-positive ratio per detector for two weeks before it is allowed to notify. A detector below ~50% PPV should downgrade to Hermes-investigates-silently, not parent-facing flag.
- Add a monthly one-line **“alarm health” self-report** in the Sunday digest: count of flags raised vs. flags the operator acted on. A falling action-rate is the leading indicator of desensitization.

-----

## Iteration 3 — Lead: Behavior-Change Scientist (Implementation Intentions & Behavioral Activation)

**Top-down.** Goal 3 (“every problem ships with a solution, never a bare problem”) is the system’s crown jewel, and it is the goal with the strongest, most direct research backing. Gollwitzer & Sheeran’s (2006, *Advances in Experimental Social Psychology*) meta-analysis of **94 independent tests (>8,000 participants)** found implementation intentions — if-then plans specifying the *when/where/how* of action — produce a medium-to-large effect on goal attainment (**d = 0.65**), one of the most robustly replicated effects in behavioral psychology; the update by Sheeran, Listrom & Gollwitzer (2024) aggregated 642 tests. Every flag ending in “Ask Hermes: draft a note to the teacher…” is a machine-generated implementation intention. This is not a nice-to-have; it is the mechanism that closes the intention-action gap.

**Bottom-up.** The “smallest next action” phrasing maps onto behavioral activation, whose evidence base for reducing depressive symptoms is strong (Ekers et al. 2014 meta-analysis, 26 RCTs, 1,524 subjects; behavioral activation superior to controls, SMD −0.74). BA’s core mechanism — reduce avoidance by shrinking the first step to something trivially doable — is exactly what “here is the one small thing to do next” delivers. The T-14/-7/-3 milestone nudges for long-runway assignments are spaced implementation-intention cues; the encouraging (not nagging) framing matters because BA works through approach, not pressure.

**Key findings.** Fully baked in for Goal 3, with the strongest evidence in the whole audit.

**Recommendations.**

- Preserve the invariant literally in `SOUL.md`: **no flag may be emitted without a populated `next_action` field**; a flag with an empty next_action is a bug, not a message. This is the one rule most worth protecting against future model drift.
- For the proactivity roadmap’s draft-handoff, ensure the draft *is* the if-then completion: the cue (flag) and the response (pre-written draft) arrive together, which is the highest-fidelity implementation intention possible.

-----

## Iteration 4 — Lead: HCI Researcher (Personal Informatics, Abandonment & the Single-Operator Problem)

**Top-down.** Goal 1 says “for both parents,” but the system depends almost entirely on one operator. This is the deepest structural risk, and the personal-informatics literature is blunt about it. Epstein, Ping, Fogarty & Munson’s (2015, *UbiComp*) Lived Informatics Model established that **lapsing and resuming are normal, expected phases of tool use, not failure** — people lapse by forgetting, by upkeep burden, by intentional skipping, and by suspending. The design question is not “will the operator lapse?” (they will — vacations, illness, burnout) but “does the system degrade gracefully when they do?”

**Bottom-up.** The “always sends at 6am” heartbeat is smart resilience engineering: silence signals breakage, so a lapse in the *machine* is visible. But there is no equivalent for a lapse in the *operator*. If the operator stops reading, nothing changes — the briefs keep sending into a void, tasks nag unanswered, and the co-operator has no escalation path. Vacation mode with a “while you were out” catch-up digest is a good partial answer for *planned* absence; there is no answer for *unplanned* disengagement. Epstein et al.’s (2016, *CHI*) “Beyond Abandonment to Next Steps” reframes abandonment as sometimes a success (“happy abandonment”), but only when the person has internalized the behavior — which argues for moving knowledge toward the humans over time, not deepening dependence on the tool.

**Key findings.** Partially baked in for Goal 1. Single-operator dependence is the top unaddressed risk.

**Recommendations.**

- Add an **operator-liveness check**: if no Telegram command (/done, /snooze, any interaction) is received for N days during school term, the 20:30 close-of-day escalates one line to the co-operator: “the operator hasn’t checked in for 3 days — want the digest routed to you until they’re back?” This makes operator-lapse visible the way machine-lapse already is.
- Build **the co-operator a real fallback interface, not just a digest** (see Iteration 7), so the system has two operators, not one operator and one reader.
- Adopt Epstein’s framing explicitly: the long-run success metric is *the operator and the kids needing the system less*, not engagement going up.

-----

## Iteration 5 — Lead: Behavioral Psychologist (Motivational Economy: Streaks, Wins, Points)

**Top-down.** Goal 4 (sticky, enjoyable) and Goal 6 (wellbeing, not scorekeeping) collide precisely here. Does the streak/wins/task-count economy create durable motivation or a subtle points-system trap? The answer is nuanced and is the most important single finding for the roadmap.

**Bottom-up.** The system does three things right and courts one specific danger.

Right #1 — **Streak breaks are silent by design (fresh start, never shame).** This is the correct reading of the streak-loss literature. The demotivation risk of streaks scales with streak length: losing a 5-day streak is trivial, losing a 500-day streak “might be enough to make someone quit entirely”  (Association for Talent Development analysis of long-run Duolingo use). Silent breakage defuses the loss-aversion bomb that Duolingo has to patch with paid “streak freezes.”

Right #2 — **Wins are always surfaced.** This is a direct countermeasure to negativity bias. Baumeister, Bratslavsky, Finkenauer & Vohs (2001, *Review of General Psychology*, 5(4):323–370, “Bad Is Stronger Than Good”) found across a broad range of phenomena that “bad emotions, bad parents, and bad feedback have more impact than good ones, and bad information is processed more thoroughly than good,”   concluding that “hardly any exceptions (indicating greater power of good) can be found.” Rozin & Royzman (2001, *Personality and Social Psychology Review*, 5(4):296–320) add *negativity dominance*: “combinations of negative and positive entities yield evaluations that are more negative than the algebraic sum of individual subjective valences would predict.” Translation: in any brief that mixes a flagged problem with three wins, the problem wins the day emotionally unless the wins are given deliberate structural weight. Surfacing wins is not sentimentality; it is required to keep the net emotional valence of the brief non-toxic so the operator keeps reading (Goal 4). The same principle underwrites the Gottman 5:1 “magic ratio” — the observation from Gottman & Levenson’s longitudinal work that stable relationships maintain roughly five positive interactions per negative one — a useful heuristic for the *tone budget* of family-facing copy, though it is a correlational marriage-lab finding, not a designed-system result, so treat it as directional rather than a target to instrument.

Right #3 — **The June year-in-review as the one place to celebrate at length**, with intermittent sparse rewards the rest of the year, matches the intermittent-reinforcement principle without gamifying daily life.

The danger — **task-completion counts as “Wins.”** Deci, Koestner & Ryan’s (1999, *Psychological Bulletin*, 125(6):627–668) meta-analysis of **128 studies** found that tangible, expected, completion-contingent rewards significantly *undermine* free-choice intrinsic motivation (completion-contingent d = −0.36; all expected tangible rewards d = −0.36; engagement-contingent d = −0.40). Critically, the exception is **verbal/informational positive feedback, which *enhanced* intrinsic motivation (free-choice d = +0.33; self-reported interest d = +0.31)** — but per Cognitive Evaluation Theory only when experienced as informational (affirming competence), not controlling. As the authors put it, “positive feedback enhanced both free-choice behavior (d = 0.33) and self-reported interest (d = 0.31).” The design implication is sharp: if “Wins” become a *counted score* the operator starts optimizing, the system risks converting their intrinsic care for their kids into extrinsic point-chasing (the overjustification effect — people “attribute their behavior to the reward and thus discount their interest in the activity”). If “Wins” stay *informational* (“the older child cleared the missing-assignment zero in Science”) they affirm competence and are protective.

**Key findings.** Partially baked in for Goals 4/6. Streak silence and win-surfacing are excellent. The latent trap is quantifying wins into a score.

**Recommendations.**

- **Keep wins as specific, named, informational statements; never render a “wins this week: 7” scoreboard.** The number is the overjustification trap; the sentence is competence-affirming feedback. Encode in `SOUL.md`: “Wins are described, never counted.”
- The proposed “outcome metrics report card” (from the 25-rec panel) is where this trap is most likely to sneak in — see What NOT to Build.
- Keep streak breaks silent; if a “streak” is ever shown, cap the displayed number or reset the visible counter periodically so no 500-day bomb accumulates.

-----

## Iteration 6 — Lead: Family-Medicine / Pediatrics Physician (Health Features & the Surveillance Line)

**Top-down.** The 25-rec panel’s hard constraint — “health features must never become surveillance/scorekeeping; food features variety-positive never restrictive; relationship features nudge never dashboard” — is exactly the right clinical instinct, and it should be treated as inviolable. The proposed sleep-compression detector, family movement ledger, and dinner variety scorecard are the highest-risk items in the entire backlog because they turn the family into quantified subjects.

**Bottom-up.** Self-care gap detection (one gentle line if next week has no farm/gym/run/bike/golf time, never guilt-phrased) is the model to emulate: it is a *cue*, not a *score*, and it is aimed at the operator’s own behavior (self-directed, autonomy-preserving) rather than surveilling others. Medical/dental recall cadences and the clothing-size seasonal audit are legitimate administrative catches — anticipation work in Daminger’s sense (Iteration 7), offloaded appropriately.

**Key findings.** Asserted, not yet code-enforced, for Goal 6 on the health side. The constraint lives in a panel document and the operator’s judgment, not in an invariant.

**Recommendations.**

- Encode the constraint as a **build-time lint on new features**: any feature whose output includes a per-person count, streak, average, or trend line over a *person* (not an admin deadline) must be rejected or reframed. “Sleep-compression detector” fails this test as a metric; it could pass only as a one-shot, non-logged, self-directed nudge to the parents (“this week is packed — consider protecting bedtime”) with no per-kid data retained.
- Food features: implement only as variety-*expanding* suggestions (new recipe, new lunch pack idea), never as a restrictive scorecard.

-----

## Iteration 7 — Lead: Family-Systems Researcher (Spousal Interface & Cognitive Labor)

**Top-down.** Goal 1’s “for both parents” runs straight into the household-labor literature. Daminger (2019, *American Sociological Review*, 84(4):609–633, “The Cognitive Dimension of Household Labor,” 70 interviews / 35 couples) defined cognitive labor as **anticipating needs, identifying options, deciding, and monitoring** — work that is “taxing but often invisible to both cognitive laborers and their partners,” and therefore “a frequent source of conflict.” Women disproportionately carry the *anticipation* and *monitoring* halves. The Daily Brief is, functionally, an *automated cognitive-labor engine*: it anticipates, surfaces options, and monitors. This is potentially transformative — it can make invisible labor visible and shareable. But it can also entrench asymmetry.

**Bottom-up.** the co-operator’s ≤15-line Sunday digest (leads with what’s handled, then key dates, pending decisions, one kid win) is thoughtfully designed *as a digest* — but a digest is a *read-only* artifact. If the operator holds the interactive system (Hermes, commands, drafts) and the co-operator holds a summary, the system has *digitized the asymmetry* Daminger warns about: one partner does the anticipating and monitoring (now via the tool), the other receives reports. The “leads with what’s handled” framing is emotionally smart (it reassures rather than assigns), but it can read as “here’s what I handled,” which risks the gratitude-economy tension Daminger’s related work describes.

**Key findings.** Partially baked in / at risk for Goal 1. The digest is a good artifact inside a potentially lopsided arrangement.

**Recommendations.**

- Give the co-operator **symmetric optional access to Hermes**, not just the digest — same “describe it in plain English, get the drafted action” concierge. Adoption asymmetry is fine (they may not use it), but *capability* asymmetry is what entrenches the labor divide.
- Add a **quarterly “who-is-carrying-what” reflection** to the digest — not a labor-tracking dashboard (that violates Goal 6’s relationship constraint), but a single reflective prompt: “This quarter the system handled X anticipations for the family. Want to rebalance any standing responsibilities?” This surfaces the invisible without scoring the partner.
- Consider letting **the co-operator set their own digest verbosity** (autonomy; see Iteration 8).

-----

## Iteration 8 — Lead: Self-Determination-Theory Psychologist (Autonomy for the Operator AND the Kids)

**Top-down.** Ryan & Deci’s self-determination theory (2000, *American Psychologist*, 55(1):68–78; 2020, *Contemporary Educational Psychology*) holds that intrinsic motivation and wellbeing require three nutrients: **autonomy, competence, relatedness**. This applies twice here — to the operator and to the kids (the ultimate subjects, Goal 6). A system that makes the operator feel controlled by their own tool, or that makes the kids feel surveilled, thwarts the very needs it should support.

**Bottom-up — the operator.** The system mostly supports the operator’s autonomy: it proposes, they dispose; nothing is autonomous; drafts are approve/edit/skip. Competence is supported by wins and by the concierge turning vague needs into exact commands. The risk is the nagging task engine: tasks that “nag every morning until /done” can shift from *informational* to *controlling*. The stale-task escalation (nagged ≥5 days → Hermes investigates once, proposes 2-3 ways out) is the right SDT move — it converts nagging (control) into support (competence + autonomy). TASK_REMIND_DAYS=5 as the escalation trigger is a reasonable default; there is no research-blessed number, but 5 school days = one week of real-world opportunity to act before the tool changes strategy, which is defensible.

**Bottom-up — the kids.** The older child’s future self-monitoring summary is the most developmentally important item in the whole backlog, and the research strongly favors it. Stattin & Kerr’s landmark reinterpretation (2000, *Child Development*, 71(4):1072–1085, 1,186 Swedish 14-year-olds) found that parental *knowledge* predicts good adolescent adjustment, but that this knowledge comes overwhelmingly from **child disclosure, not parental tracking/surveillance** — in replications, child disclosure alone predicted ~44% of variance in parental knowledge (child-report), with solicitation and control adding only ~3–5%. Their sharper finding: parental *control* efforts were linked to *poor* adjustment once the child’s feeling of being controlled was accounted for. The prevention implication is to build “trusting parent-child relationships that are facilitative of honest self-disclosure rather than training parents to more effectively track.” The older child is in early adolescence — exactly the window where surveillance backfires and disclosure protects.

**Key findings.** Partially baked in for Goal 6. The design already leans toward “move to kid self-monitoring” and “verify before confronting, never raise a 4pm flag at pickup” — these are precisely the Stattin/Kerr-aligned guardrails. But they are asserted in the psych doc, enforced by the operator.

**Recommendations.**

- **Prioritize the older child’s own weekly summary** ahead of most of the 25-rec backlog. Give it to *the older child*, framed as his tool for his competence, with him controlling what (if anything) is shared up to parents. This operationalizes disclosure-over-surveillance and supports all three SDT needs for him.
- Encode the guardrail as behavior, not just prose: **the pipeline must never surface a kid-behavior flag to a parent in a pickup/transition window**; batch it to the standup/close-of-day. This is the “never raise a 4pm flag at pickup” rule made mechanical.
- For the operator: keep every task nag ending in an action, and let the ≥5-day escalation always offer a “kill this task” option (autonomy to abandon a goal is itself SDT-supportive).

-----

## Iteration 9 — Lead: Automation-Trust / Human-Factors Engineer

**Top-down.** Goal 5 (proactive, options-with-approval, never autonomous) is a trust-calibration problem, and Parasuraman & Riley’s foundational taxonomy (1997, *Human Factors*, 39(2):230–253, “Humans and Automation: Use, Misuse, Disuse, Abuse”) is the map. **Misuse** = overreliance/complacency (the human stops checking because the automation has been reliable — complacency “grows precisely when the automation has been reliable in the past”). **Disuse** = abandoning good automation because of false alarms (the alarm-fatigue path). **Abuse** = deploying automation without regard for the human’s role. The system must thread between misuse and disuse.

**Bottom-up.** The “code decides, model narrates” split is the strongest anti-abuse decision in the architecture: the LLM (the least predictable component) never detects or decides; it narrates and drafts, and even its two extractors are “add-only” and validated. This bounds the blast radius of a model error. The approve/edit/skip draft-handoff is the correct anti-misuse design: it keeps the operator in the loop as decider, preventing the complacency drift Parasuraman warns about. The code-review items are, in human-factors terms, the *disuse* risks: C1 (LMS pagination → silent truncation), C2 (nested gradebook-portal errors pass guards), C3 (failed fetch poisons baseline), C12 (one malformed source kills the whole brief) are all cases where the automation silently fails or mis-fires — exactly what erodes trust and drives disuse. C10 (LMS→kid attribution by first-name string match) is a *misuse* risk: a subtle wrong attribution the human may not catch.

**Key findings.** Roadmap partially baked in for Goal 5. The choice-architecture detail (2-3 pre-researched options) is well-chosen: Iyengar & Lepper’s (2000, *Journal of Personality and Social Psychology*, 79(6):995–1006) jam study (6 options → 30% purchased; 24 options → 3%) and the broader choice-overload literature support small option sets; 2-3 options is comfortably in the “supports decision” range, not the “paralysis” range. The Saturday-night decision-research job delivering Sunday decisions with 2-3 pre-researched options with tradeoffs is choice architecture done right.

**Recommendations.**

- Treat C1, C3, C12 as **must-fix before any roadmap expansion** — they are the silent-failure paths that “silence is earned” promises to catch but currently can’t (a truncated the LMS page or poisoned baseline produces confident, wrong silence). This is the highest-priority engineering work in the audit.
- C10 (first-name attribution): add a **collision guard** — if two kids could match, flag rather than guess. A wrong-kid attribution is a trust-killing misuse error.
- Keep option sets at 2-3; never let the decision-research job return 5+ options (choice overload). Include a “do nothing / defer” option explicitly — omitting the null option is a known choice-architecture bias.
- To counter long-run complacency, have Hermes **periodically show its work** (once a month, one flag includes the underlying data), so the operator recalibrates trust rather than defaulting to blind acceptance.

-----

## Iteration 10 — Lead: Efficiency/Productivity Expert (PIM, Checklists, Task Management)

**Top-down.** Goal 4 (effective, efficient) and the Todoist projection decision live here. Whittaker & Sidner’s (1996, *CHI*, 276–283) “Email Overload” — the founding PIM paper — showed email becomes overloaded because it serves three jobs at once: communication, task management, and archiving. The Daily Brief deliberately *unbundles* these: detection/state is the pipeline (single source of truth), Todoist is a *projection* for viewing, email/Telegram is communication. This is the correct PIM architecture and avoids the overload trap.

**Bottom-up.** The four horizon-based Todoist projects (⚡Today max 8 / 📅This Week / ⏳Waiting On / 🗄️Later) with silent rollover and a third-rollover tripwire is a clean GTD-style horizon model. The 8-item Today cap is well-chosen: it sits at the edge of working-memory capacity and forces prioritization. Bellotti et al.‘s Taskmaster work and the broader task-management literature support task-centric (not message-centric) organization. The checklist framing is validated by Gawande’s *Checklist Manifesto* and its two anchor cases: the WHO Safe Surgery Checklist, which in Haynes et al.’s 8-hospital global trial (*NEJM* 2009, 3,733 patients before / 3,955 after) cut inpatient deaths from 1.5% to 0.8% and major complications from 11% to 7% — reducing rates of death and complications “by more than one-third”; and the Michigan Keystone (Pronovost) Project, whose five-item central-line checklist was found to reduce catheter-related bloodstream infection by roughly two-thirds in a statewide study. Checklists work by catching “errors of ineptitude” (we know what to do but skip a step), which is exactly the “permission slip due Friday” failure mode the pipeline prevents.

The third-rollover tripwire (do today / snooze / delegate / kill) is excellent: it is the mechanism that prevents Todoist from becoming a graveyard of stale tasks — the single most common way task systems die (Whittaker’s abandonment work; the “failed folders” phenomenon).

**Key findings.** Fully baked in for Goal 4 (efficient/effective). The maintenance-burden risk is the projection sync (two systems can diverge).

**Recommendations.**

- Keep the pipeline as single source of truth; treat **Todoist divergence as a bug** — a nightly reconciliation check that flags (silently, to logs) any Today item in Todoist not backed by pipeline state. Dual-source-of-truth is the classic PIM failure.
- Keep the 8-item cap hard; when exceeded, the “+N more” with forced triage is correct — never show 20 items.
- The third-rollover tripwire should itself ship with the smallest-next-action framing (each option one tap).

-----

## Iteration 11 — Lead: Sleep/Circadian Physician (The Sunday Ritual & Evening Cutoffs)

**Top-down.** The Sunday 19:30–19:55 review + 20:30 close-of-day + hard rule “no task review after 21:00” is, whether by intent or instinct, an evidence-aligned sleep-protection design. Goal 4 (enjoyable, sticky) and Goal 6 (wellbeing) both depend on the system not wrecking the operator’s sleep with late-night administrative arousal.

**Bottom-up.** The pre-sleep cognitive arousal literature is clear and directly relevant. Kalmbach, Buysse, Cheng, Roth, Yang & Drake (2020, *Sleep Medicine*, 71:151–160) found nocturnal cognitive arousal — rumination, worry, “rehearsal and planning” (“what happened today and what I’ve got on tomorrow”) — was *more robustly* associated with objective sleep disturbance and 24-hour hyperarousal than insomnia diagnosis or depressive symptoms. Reviewing tasks, decisions, and tomorrow’s logistics is precisely “rehearsal and planning” cognition. Doing it at 20:30 and hard-stopping at 21:00 keeps the arousal window well clear of sleep onset. The “planner arrives 18:00 → dinner → review with planner + Todoist open → 20:30 close-of-day is the last touch” sequence front-loads the cognitively arousing work into the evening and ends on a deliberate closing ritual — this is textbook “cognitive offloading before bed” (write it down so you stop rehearsing it), a recognized countermeasure to pre-sleep rumination.

**Key findings.** Fully baked in for Goal 4/6 on the sleep dimension — one of the quietly best decisions in the system.

**Recommendations.**

- Keep the 21:00 hard stop and consider making it *enforced*: Hermes should **decline task-review requests after 21:00** (“that’ll keep you up — it’s parked for the 6am brief”), converting a norm into a boundary.
- The close-of-day message should **end on a closing/decompressing note** (the day’s kid win, “nothing pending — you’re clear”), not on an open problem, so the last cognition before bed is closure, not an open loop (unclosed loops drive rumination).

-----

## Iteration 12 — Lead: Busy-Parent User Panel (Lived Experience) + Maintenance-Burden Synthesis

**Top-down.** The parent panel’s verdict on Goal 4 (sticky, enjoyable): the system is *only* sticky if it stays out of the way. Every parent on the panel had abandoned a family app because it demanded more than it returned. This is the maintenance-burden problem, and it is the frame for the whole roadmap.

**Bottom-up — the feature-creep tension (Goal 4 vs. the 25-rec backlog).** The system has ~20 features, 139 tests, and 25 more recommendations. The personal-informatics abandonment literature (Epstein et al. 2015/2016) identifies **upkeep burden** as a primary cause of lapsing — people quit tools that require managing. Every added feature adds: a parser to maintain, a verification-debt item, a possible false-fire, and a notification-slot pressure. The system’s own rules — “new features must replace notification slots, not add them,” and the ≤4/day cut line — are the antibodies. But 25 recommendations is a backlog that, if built, would violate the system’s own minimalism. **The panel’s strong recommendation: the correct number of the 25 to build in the next year is small — likely 3-5, not 25.** The build order (measurement first, then health-admin, then relationship nudges) is sensible sequencing, but sequencing 25 features is still 25 features.

**Key findings.** This is the report’s central strategic warning. The system’s minimalism is its moat; the backlog is the threat to the moat.

**Recommendations.**

- Adopt a **hard feature budget**: for every new feature shipped, one existing feature must be retired or one notification slot freed. Net feature count flat or declining.
- Build only the backlog items that *remove* parent cognitive load without adding parent-facing surface: the strongest candidates are the older child’s self-monitoring summary (Iteration 8), illness mode /sick command (a mode, not a feature-stream), and the guided Sunday review (formalizes an existing ritual). Most of the rest should wait or be rejected (see below).
- Re-run the September ping-audit as a *feature* audit too: any feature that hasn’t been used/acted-on in a term is a retirement candidate (the “device in the drawer” — Epstein 2016).

-----

## Consolidated, Prioritized Recommendations

### MUST-FIX (do before any expansion)

1. **Fix silent-failure paths C1, C3, C12** (LMS pagination truncation, poisoned baseline, one-bad-source-kills-brief). *Serves Goal 1. Backing: Parasuraman & Riley 1997 (disuse from silent failure); the system’s own “silence is earned” invariant. Size: targeted engineering, high priority, each isolatable.*
1. **C10 kid-attribution collision guard** — flag, don’t guess, on ambiguous first-name matches. *Serves Goals 1, 6. Backing: Parasuraman & Riley (misuse/undetected error). Size: small.*
1. **Encode “no flag without a next_action” as a hard invariant in SOUL.md.** *Serves Goal 3. Backing: Gollwitzer & Sheeran 2006 (d=0.65, 94 tests, >8,000 participants). Size: trivial to state, essential to protect.*
1. **PPV-quarantine every verification-debt parser** until it proves specificity on live data. *Serves Goals 2, 3. Backing: alarm-fatigue PPV literature (AHRQ PSNet — 80–99% of ECG alarms false/insignificant). Size: medium (per-detector logging).*

### SHOULD-FIX

1. **Operator-liveness escalation to the co-operator** after N days of no interaction. *Serves Goal 1. Backing: Epstein et al. 2015 (lapsing is normal; design for it). Size: small.*
1. **Give the co-operator symmetric Hermes access**, not just the digest. *Serves Goal 1. Backing: Daminger 2019 (cognitive-labor asymmetry). Size: small (access, not new build).*
1. **“Wins are described, never counted”** — ban any wins scoreboard. *Serves Goals 4, 6. Backing: Deci, Koestner & Ryan 1999 (completion-contingent reward d=−0.36; verbal/informational feedback d=+0.33). Size: trivial rule, prevents a class of future features.*
1. **Feature-lint against per-person metrics** (counts/streaks/trends over a person). *Serves Goal 6. Backing: the panel’s own constraint + SDT (Ryan & Deci 2000). Size: process rule.*
1. **Never surface kid-behavior flags in pickup/transition windows** — batch to standup/close-of-day. *Serves Goal 6. Backing: Stattin & Kerr 2000 (surveillance backfires); the existing psych guardrail. Size: small (time-window gate).*
1. **Enforce the 21:00 hard stop in Hermes’ behavior** and end close-of-day on closure, not an open loop. *Serves Goals 4, 6. Backing: Kalmbach et al. 2020 (pre-sleep cognitive arousal). Size: small.*

### CONSIDER

1. **Prioritize the older child’s self-monitoring summary**, owned by the older child. *Serves Goal 6. Backing: Stattin & Kerr 2000; Ryan & Deci 2000. Size: medium; highest developmental value in backlog.*
1. **Quarterly cognitive-labor reflection prompt** in the digest (reflective, not a dashboard). *Serves Goal 1. Backing: Daminger 2019. Size: small.*
1. **Monthly “alarm health” self-report** (flags raised vs. acted-on) as a desensitization early-warning. *Serves Goal 2. Backing: alarm-fatigue desensitization literature (AHRQ PSNet). Size: small.*
1. **Rename the ping counter “context-switches induced.”** *Serves Goal 2. Backing: Mark et al. 2005/2008. Size: trivial.*
1. **Monthly “show your work” from Hermes** to keep trust calibrated. *Serves Goal 5. Backing: Parasuraman & Riley 1997 (complacency). Size: small.*

### EXPLICITLY REJECT (with reasoning)

- **Outcome-metrics “report card” as a quantified scoreboard** → *Rejected. Deci/Koestner/Ryan 1999 (overjustification) + the family-as-quantified-subject risk (Goal 6). If measurement is needed, keep it private to the system for tuning, never as a family-facing score.*
- **Dinner variety scorecard, family movement ledger, sleep-compression detector as logged metrics** → *Rejected as metrics; permissible only as one-shot, non-retained, self-directed nudges. Backing: Goal 6 hard constraint; SDT autonomy-thwarting; surveillance-shape risk.*
- **Equity snapshot of household labor as a dashboard** → *Rejected as a dashboard (violates “relationship features nudge, never dashboard” and risks the gratitude-economy conflict Daminger documents). Permissible only as the reflective prompt in #12.*
- **Building most of the 25 recommendations in the next year** → *Rejected on maintenance-burden grounds (Epstein 2015/2016). Cap at 3-5, net feature count flat.*

-----

## What NOT to Build

The strongest recommendation in this audit is a restraint recommendation. The system works *because* it is disciplined about silence and small about surface area. The research on abandonment (Epstein et al., 2015/2016 — upkeep burden drives lapsing) and email/PIM overload (Whittaker & Sidner, 1996 — tools die when overloaded with jobs) points the same direction: **the failure mode for this system is not “missed a permission slip.” It is “became a second job, then a dreaded chore, then a device in the drawer.”**

Do not build:

- **Any per-person score, streak-count, average, or trend line.** These convert care into scorekeeping (Deci/Koestner/Ryan 1999) and family members into surveilled subjects (Stattin & Kerr 2000). The system’s own psych review already names “vigilance systems become dreaded then avoided” — a scoreboard is the fastest path there.
- **New notification slots.** The ≤4/day budget and the “replace, don’t add” rule are the system’s antibodies against alarm fatigue (AHRQ PSNet). Honor them absolutely.
- **Health features that log rather than nudge.** The line between “one gentle self-care line” (good) and “sleep-compression detector with a trend” (surveillance) is the line between a supported and a thwarted autonomy need (SDT).
- **A richer the co-operator *digest* in place of the co-operator *access*.** Fixing asymmetry with a better report deepens the asymmetry (Daminger 2019). Give capability, not more paper.
- **Features that require ongoing parent maintenance.** Every parser is a liability; every feature that needs curation is a lapse risk (Epstein 2016).

The panel’s consensus: **spend the next year on hardening (must-fix engineering), consolidation (the 20:30 merge, the September audit), and the two or three highest-value load-*removing* features (the older child’s summary, illness mode, guided Sunday review). Reject or defer the rest.**

-----

## One-Page Operating Philosophy (paste into docs; written to survive a less-capable future model)

**Daily Brief + Hermes — Principles That Must Not Change**

1. **Code decides; the model narrates.** All detection is deterministic. The LLM may explain, draft, and propose — never detect, decide, or act autonomously. This bounds the damage any model error can do. Do not let a future model move detection logic into the model.
1. **Silence is earned.** A silent channel means “nothing needed,” and the system must *guarantee* that by making every silent-failure path alert loudly with a suggested fix. If the system breaks, it must say so. Never let silence become ambiguous.
1. **No problem without its smallest next action.** Every flag ends with the one small thing to do next, ideally a ready-to-approve draft. A bare problem is a bug. (This is the system’s single highest-value rule; it is an implementation intention — worth a medium-to-large effect, d≈0.65, on whether the thing actually gets done.)
1. **Noise is budgeted; attention is the scarce resource.** Cap notifications (≤4 context-switches/day), batch to a few predictable sends, cap displayed items (8/12 + “+N more”), and never add a notification slot without removing one. More alerts do not mean more safety — they mean desensitization and eventual abandonment.
1. **Wins are described, never counted.** Surface specific, named wins to counteract negativity bias (bad is stronger than good; a mixed brief reads net-negative without deliberate wins). But never render a score, streak count, or leaderboard — counting turns care into point-chasing and undermines the intrinsic motivation the whole system serves.
1. **Knowledge, not surveillance — especially for the kids.** Parental knowledge protects kids; parental *surveillance* backfires as they grow. Move toward the kids monitoring themselves and choosing to share. Never surface a kid-behavior flag at pickup or in a transition moment. Verify before confronting. Praise recoveries specifically.
1. **Two operators, not one operator and one reader.** Both parents get capability, not just one getting a report. Design for the operator lapsing (vacation, illness, burnout) the same way you design for the machine breaking — make it visible and route around it.
1. **Protect sleep and ritual.** Warm, celebratory Sunday planner; terse daily wire copy. Hard stop on task-review by night; end the day on closure, not an open loop.
1. **The goal is to be needed less.** Success is the family carrying less invisible load and the kids growing more capable — not engagement going up. If a feature makes the system more central and the humans more dependent, question it.
1. **When in doubt, do less.** This system works because it is small and disciplined. Every proposed feature is a maintenance liability and an abandonment risk. Build only what removes load. Reject scoreboards, dashboards, and anything that turns the family into data.

-----

## Research Bibliography

- Baumeister, R. F., Bratslavsky, E., Finkenauer, C., & Vohs, K. D. (2001). Bad is stronger than good. *Review of General Psychology*, 5(4), 323–370.
- Bellotti, V., Ducheneaut, N., Howard, M., & Smith, I. (2003). Taking email to task: the design and evaluation of a task management centered email tool. *CHI*.
- Daminger, A. (2019). The cognitive dimension of household labor. *American Sociological Review*, 84(4), 609–633.
- Deci, E. L., Koestner, R., & Ryan, R. M. (1999). A meta-analytic review of experiments examining the effects of extrinsic rewards on intrinsic motivation. *Psychological Bulletin*, 125(6), 627–668.
- Ekers, D., Webster, L., Van Straten, A., Cuijpers, P., Richards, D., & Gilbody, S. (2014). Behavioural activation for depression: an update of meta-analysis of effectiveness and subgroup analysis. *PLoS ONE*, 9(6), e100100.
- Epstein, D. A., Ping, A., Fogarty, J., & Munson, S. A. (2015). A lived informatics model of personal informatics. *UbiComp*.
- Epstein, D. A., Caraway, M., Johnston, C., Ping, A., Fogarty, J., & Munson, S. A. (2016). Beyond abandonment to next steps: understanding and designing for life after personal informatics tool use. *CHI*. (See also Epstein et al., 2016, “Reconsidering the Device in the Drawer: Lapses as a Design Opportunity,” *UbiComp*.)
- Fitz, N., Kushlev, K., Jagannathan, R., Lewis, T., Paliwal, D., & Ariely, D. (2019). Batching smartphone notifications can improve well-being. *Computers in Human Behavior*, 101, 84–94.
- Gawande, A. (2009). *The Checklist Manifesto: How to Get Things Right.*
- Haynes, A. B., Weiser, T. G., Berry, W. R., et al. (2009). A surgical safety checklist to reduce morbidity and mortality in a global population. *New England Journal of Medicine*, 360(5), 491–499.
- Gollwitzer, P. M., & Sheeran, P. (2006). Implementation intentions and goal achievement: a meta-analysis of effects and processes. *Advances in Experimental Social Psychology*, 38, 69–119. (Sheeran, Listrom & Gollwitzer, 2024 update, 642 tests.)
- Gottman, J., & Levenson, R. W. (longitudinal studies, 1970s–1990s; the 5:1 “magic ratio”).
- Iyengar, S. S., & Lepper, M. R. (2000). When choice is demotivating: can one desire too much of a good thing? *Journal of Personality and Social Psychology*, 79(6), 995–1006. (The “jam study.”)
- Kalmbach, D. A., Buysse, D. J., Cheng, P., Roth, T., Yang, A., & Drake, C. L. (2020). Nocturnal cognitive arousal is associated with objective sleep disturbance and indicators of physiologic hyperarousal in good sleepers and individuals with insomnia disorder. *Sleep Medicine*, 71, 151–160.
- Lally, P., van Jaarsveld, C. H. M., Potts, H. W. W., & Wardle, J. (2010). How are habits formed: modelling habit formation in the real world. *European Journal of Social Psychology*, 40(6), 998–1009.
- Mark, G., Gonzalez, V. M., & Harris, J. (2005). No task left behind? Examining the nature of fragmented work. *CHI*. (Mark, Gudith & Klocke, 2008, “The Cost of Interrupted Work: More Speed and Stress,” *CHI*.)
- Parasuraman, R., & Riley, V. (1997). Humans and automation: use, misuse, disuse, abuse. *Human Factors*, 39(2), 230–253. (Parasuraman & Manzey, 2010, complacency and bias in human use of automation, *Human Factors*, 52(3), 381–410.)
- Pronovost, P., et al. (2006). An intervention to decrease catheter-related bloodstream infections in the ICU (Michigan Keystone Project). *New England Journal of Medicine*, 355(26), 2725–2732.
- Rozin, P., & Royzman, E. B. (2001). Negativity bias, negativity dominance, and contagion. *Personality and Social Psychology Review*, 5(4), 296–320.
- Ryan, R. M., & Deci, E. L. (2000). Self-determination theory and the facilitation of intrinsic motivation, social development, and well-being. *American Psychologist*, 55(1), 68–78. (Ryan & Deci, 2020, *Contemporary Educational Psychology*, 61, 101860.)
- Stattin, H., & Kerr, M. (2000). Parental monitoring: a reinterpretation. *Child Development*, 71(4), 1072–1085. (Kerr & Stattin, 2000; Kerr, Stattin & Burk, 2010, *Journal of Research on Adolescence*.)
- Whittaker, S., & Sidner, C. (1996). Email overload: exploring personal information management of email. *CHI*, 276–283. (Whittaker, 2011, “Personal information management: from information consumption to curation,” *Annual Review of Information Science and Technology*, 45(1).)
- AHRQ PSNet (Jacques, S., & Williams, E., 2016). *Reducing the Safety Hazards of Monitor Alert and Alarm Fatigue.* (Alarm-fatigue mechanism; 80–99% of ECG alarms false/clinically insignificant.)