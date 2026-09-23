> **Conversion note:** Clean H1/H2/H3 hierarchy for auto-TOC and internal hyperlinks; external links are live DOIs/publisher pages verified during research. Add cover + auto-TOC at conversion.

# Daily Brief + Hermes: Design Choices and Their Evidence Base

## Abstract

A household vigilance system can easily become a source of dread: more alerts, more guilt, more surveillance, worse sleep. This paper documents the choices made to prevent that, and grounds each in published research. The through-line: budget attention, earn silence, always attach a next action, describe wins without counting them, favor knowledge over surveillance, keep the human in the loop, and protect sleep and ritual. Where the evidence is strong we say so; where a parameter is a reasoned default rather than a proven threshold, we say that too.

## 1. Introduction — the vigilance-system dread problem

Monitoring systems fail not by missing events but by producing so many low-value signals that the operator tunes out (or burns out). The design here treats attention as the scarce resource and the family’s sleep and relationships as things to protect, not optimize against.

## 2. Batched fixed-time sends + a ≤4 context-switch budget

**Choice:** fixed 6:00 / 16:00 / 20:30 sends, capped at ≤4 context-switches/day. **Rejected:** real-time push per event; also fully-off. **Evidence:** Fitz, Kushlev, Jagannathan, Lewis, Paliwal & Ariely (2019) ran a randomized field experiment (n=237) in which the three-times-daily batching condition outperformed both continuous notifications and notifications-off — the off group actually reported higher anxiety and FoMO (*Computers in Human Behavior* 101:84–94, <https://doi.org/10.1016/j.chb.2019.07.016>). Interruption cost motivates the cap: Gloria Mark’s work found it takes about **23 minutes 15 seconds** to return to the original task after an interruption; the follow-up lab study (Mark, Gudith & Klocke, CHI ’08) found interrupted workers finished ~7% faster but with “more stress, higher frustration, time pressure and effort”  (<https://doi.org/10.1145/1357054.1357072>; foundational field study Mark, Gonzalez & Harris, CHI ’05, <https://doi.org/10.1145/1054972.1055017>).

## 3. Silence is earned + PPV quarantine

**Choice:** scheduled channels stay silent when nothing is true; every blind-built parser must earn ≥50% PPV on two weeks of live data before it may notify. **Rejected:** ship detectors straight to notifying. **Evidence:** AHRQ PSNet’s alarm-fatigue primer (Jacques & Williams, 2016) states “Research has shown that 80%–99% of ECG monitor alarms are false or clinically insignificant”  — desensitization from false alarms is the failure mode we design against (<https://psnet.ahrq.gov/perspective/reducing-safety-hazards-monitor-alert-and-alarm-fatigue>). Parasuraman & Riley (below) tie disuse of automation directly to false-alarm rates and base-rate neglect.

## 4. No flag without a next action + draft handoff

**Choice:** every surfaced problem carries a concrete smallest next step; where a reply is needed, the pipeline hands Hermes a job and Hermes drafts it. **Rejected:** notify-and-leave-it-to-you. **Evidence:** Gollwitzer & Sheeran (2006) meta-analyzed 94 tests and found implementation intentions (“if situation Y, then I will do X”) produce a medium-to-large effect on goal attainment, **d = 0.65** (*Adv Exp Soc Psych* 38:69–119, <https://doi.org/10.1016/S0065-2601(06)38002-1>). Behavioural activation — acting via small concrete steps — is an effective treatment for depression in Ekers et al. (2014), SMD −0.74 vs controls (*PLoS ONE* 9(6):e100100, <https://doi.org/10.1371/journal.pone.0100100>), supporting the “smallest next action” framing.

## 5. Wins surfaced, described never counted

**Choice:** surface wins and cleared zeros in prose; never tally or score them. **Rejected:** streak counters, points, leaderboards. **Evidence:** negativity outweighs positivity, so wins must be actively surfaced — Baumeister, Bratslavsky, Finkenauer & Vohs (2001) “Bad Is Stronger Than Good” (*Rev Gen Psych* 5(4):323–370, <https://doi.org/10.1037/1089-2680.5.4.323>) and Rozin & Royzman (2001) on negativity bias and dominance (*PSPR* 5(4):296–320, <https://doi.org/10.1207/S15327957PSPR0504_2>). But *counting* backfires: Deci, Koestner & Ryan (1999) meta-analyzed 128 studies and found tangible/expected rewards undermine intrinsic motivation (free-choice d ≈ −0.36) (*Psych Bulletin* 125(6):627–668, <https://doi.org/10.1037/0033-2909.125.6.627>) — hence describe, don’t score.

## 6. Silent streak breaks

**Choice:** when a streak breaks, log it, don’t broadcast it. **Rejected:** streak-loss notifications. **Evidence:** consistent with Deci/Koestner/Ryan (above) on extrinsic contingencies and with widely reported demotivation following visible streak loss in habit apps; treated as directional, not a single-study proof.

## 7. Knowledge not surveillance + kid self-monitoring + no pickup flags

**Choice:** never flag kid behavior at pickup; verify before confronting; move toward the child self-monitoring. **Rejected:** real-time behavioral tracking/alerts on kids. **Evidence:** Stattin & Kerr (2000) reinterpreted “parental monitoring”: in 703 Swedish 14-year-olds, parental *knowledge* came mainly from the child’s voluntary disclosure, and disclosure — not tracking/surveillance — was most protective; “tracking and surveillance is not the best prescription” (*Child Development* 71(4):1072–1085, <https://doi.org/10.1111/1467-8624.00210>). Ryan & Deci (2000) SDT explains why: autonomy, competence, relatedness are needed for internalization — task escalation converts control into support (*American Psychologist* 55(1):68–78, <https://doi.org/10.1037/0003-066X.55.1.68>).

## 8. Code decides, model narrates + approve/edit/skip + show-your-work

**Choice:** deterministic detection; the model narrates and drafts; humans approve; Hermes shows its work monthly. **Rejected:** let the model detect and act. **Evidence:** Parasuraman & Riley (1997) map use/misuse/disuse/abuse of automation — misuse is over-reliance, disuse follows false alarms, and observability/appropriate trust are the fixes (*Human Factors* 39(2):230–253, <https://doi.org/10.1518/001872097778543886>). This grounds the C-fix priority (reliability first), the human-in-the-loop split, and show-your-work.

## 9. Two-or-three options with an explicit do-nothing

**Choice:** Saturday research and reviews present 2–3 pre-researched options plus an explicit do-nothing. **Rejected:** an open-ended menu. **Evidence:** Iyengar & Lepper (2000) — the jam study — showed a limited array (6) produced far more action and satisfaction than an extensive one (24/30) across three studies   (*JPSP* 79(6):995–1006, <https://doi.org/10.1037/0022-3514.79.6.995>).

## 10. Operator-liveness + feature budget + be-needed-less

**Choice:** detect operator disengagement and offer to route to the second operator; budget features; aim to be needed less. **Rejected:** assume perpetual engagement; accumulate features. **Evidence:** Epstein, Ping, Fogarty & Munson (2015) “lived informatics” documents lapsing/resuming as normal, not failure  (*UbiComp ’15* 731–742, <https://doi.org/10.1145/2750858.2804250>); Epstein et al. (2016) “Beyond Abandonment to Next Steps” designs for life after tool use  (*CHI ’16* 1109–1113, <https://doi.org/10.1145/2858036.2858045>).

## 11. Second-operator symmetric access + reflection, not a dashboard

**Choice:** the second operator is a full operator; the cognitive-labor prompt is a reflective one-liner, never a scoreboard. **Rejected:** an equity dashboard. **Evidence:** Daminger (2019) defines cognitive labor — anticipating, identifying, deciding, monitoring — as taxing and invisible, and gendered;  a dashboard would surveil rather than share it (*ASR* 84(4):609–633, <https://doi.org/10.1177/0003122419859007>).

## 12. Unbundling pipeline / Todoist / Telegram

**Choice:** keep monitoring (pipeline), task projection (Todoist), and interaction (Telegram) as distinct channels. **Rejected:** one overloaded channel (e.g., email as task-manager + archive + inbox). **Evidence:** Whittaker & Sidner (1996) coined “email overload,” showing a single channel forced to do communication + task management + archiving fails at all three (*CHI ’96* 276–283, <https://doi.org/10.1145/238386.238530>).

## 13. 20:30 close + 21:00 stop + end-on-closure

**Choice:** consolidate into a 20:30 close-of-day that ends on closure; hard-stop task review at 21:00. **Rejected:** late-evening problem-surfacing. **Evidence:** Kalmbach et al. (2020) found nocturnal cognitive arousal was more strongly tied to objectively disturbed sleep than insomnia diagnosis or depression, and was most linked to difficulty falling asleep (*Sleep Medicine* 71:151–160, <https://doi.org/10.1016/j.sleep.2019.11.1184>) — so the last thing you read at night must not be an open problem.

## 14. Checklist framing for due-tomorrow and gear

**Choice:** render tomorrow’s obligations and gear as explicit checklists. **Rejected:** prose reminders only. **Evidence:** Haynes et al. (2009) — a 19-item surgical checklist cut in-hospital death from 1.5% to 0.8% (P=0.003) and complications from 11.0% to 7.0% (P<0.001)  across eight hospitals (*NEJM* 360(5):491–499, <https://doi.org/10.1056/NEJMsa0810119>); Pronovost et al. (2006) — the Michigan Keystone checklist drove median catheter-related bloodstream infection from 2.7 per 1000 catheter-days to 0 within three months,  sustained ~18 months (*NEJM* 355(26):2725–2732, <https://doi.org/10.1056/NEJMoa061115>).

## 15. Sunday ritual anchoring

**Choice:** anchor the weekly review to a fixed Sunday ritual (18:00 planner → 19:30 review). **Rejected:** ad-hoc review whenever. **Evidence:** Lally et al. (2010) modeled real-world habit formation in 96 volunteers;  automaticity took a median of **66 days** (range 18–254) and, importantly, missing a single day did not break the curve  — anchoring to a stable cue is what builds durability (*Eur J Soc Psych* 40(6):998–1009, <https://doi.org/10.1002/ejsp.674>).

## 16. Honest limitations

- **The 4/day context-switch budget is a reasoned default, not a proven threshold.** Fitz supports batching and predictability; it does not certify “4” specifically.
- **Gottman’s 5:1 positive-to-negative ratio is directional** here (tone budget), not a measured target for a notification system.
- **Most evidence comes from other settings.** Alarm fatigue is clinical; interruption costs are knowledge-work; checklists are surgical; SDT/monitoring studies are population-level. They generalize *directionally* to one family, not as guaranteed effect sizes.
- **Streak-loss demotivation** is drawn from applied/industry analysis plus the reward literature, not a single controlled trial.
- The design’s success metric is behavioral: **is the system needed less over time?** — consistent with the lived-informatics view that lapsing is normal.

## Bibliography (verified, live links)

- Fitz, N., Kushlev, K., Jagannathan, R., Lewis, T., Paliwal, D., & Ariely, D. (2019). *Computers in Human Behavior* 101:84–94. <https://doi.org/10.1016/j.chb.2019.07.016>
- Mark, G., Gonzalez, V. M., & Harris, J. (2005). *CHI ’05* 321–330. <https://doi.org/10.1145/1054972.1055017>
- Mark, G., Gudith, D., & Klocke, U. (2008). *CHI ’08* 107–110. <https://doi.org/10.1145/1357054.1357072>
- Jacques, S., & Williams, E. (2016). AHRQ PSNet. <https://psnet.ahrq.gov/perspective/reducing-safety-hazards-monitor-alert-and-alarm-fatigue>
- Gollwitzer, P. M., & Sheeran, P. (2006). *Adv Exp Soc Psych* 38:69–119. <https://doi.org/10.1016/S0065-2601(06)38002-1>
- Ekers, D., et al. (2014). *PLoS ONE* 9(6):e100100. <https://doi.org/10.1371/journal.pone.0100100>
- Baumeister, R. F., et al. (2001). *Rev Gen Psych* 5(4):323–370. <https://doi.org/10.1037/1089-2680.5.4.323>
- Rozin, P., & Royzman, E. B. (2001). *PSPR* 5(4):296–320. <https://doi.org/10.1207/S15327957PSPR0504_2>
- Deci, E. L., Koestner, R., & Ryan, R. M. (1999). *Psych Bulletin* 125(6):627–668. <https://doi.org/10.1037/0033-2909.125.6.627>
- Stattin, H., & Kerr, M. (2000). *Child Development* 71(4):1072–1085. <https://doi.org/10.1111/1467-8624.00210>
- Ryan, R. M., & Deci, E. L. (2000). *American Psychologist* 55(1):68–78. <https://doi.org/10.1037/0003-066X.55.1.68>
- Parasuraman, R., & Riley, V. (1997). *Human Factors* 39(2):230–253. <https://doi.org/10.1518/001872097778543886>
- Iyengar, S. S., & Lepper, M. R. (2000). *JPSP* 79(6):995–1006. <https://doi.org/10.1037/0022-3514.79.6.995>
- Epstein, D. A., Ping, A., Fogarty, J., & Munson, S. A. (2015). *UbiComp ’15* 731–742. <https://doi.org/10.1145/2750858.2804250>
- Epstein, D. A., et al. (2016). *CHI ’16* 1109–1113. <https://doi.org/10.1145/2858036.2858045>
- Daminger, A. (2019). *American Sociological Review* 84(4):609–633. <https://doi.org/10.1177/0003122419859007>
- Whittaker, S., & Sidner, C. (1996). *CHI ’96* 276–283. <https://doi.org/10.1145/238386.238530>
- Kalmbach, D. A., et al. (2020). *Sleep Medicine* 71:151–160. <https://doi.org/10.1016/j.sleep.2019.11.1184>
- Haynes, A. B., et al. (2009). *NEJM* 360(5):491–499. <https://doi.org/10.1056/NEJMsa0810119>
- Pronovost, P., et al. (2006). *NEJM* 355(26):2725–2732. <https://doi.org/10.1056/NEJMoa061115>
- Lally, P., van Jaarsveld, C. H. M., Potts, H. W. W., & Wardle, J. (2010). *Eur J Soc Psych* 40(6):998–1009. <https://doi.org/10.1002/ejsp.674>
