# Cross-Functional Review + 25 Recommendations

> **Note on this public edition — read this first.** This document is **unsourced
> expert-persona analysis**: a language model was asked to review the system from
> eight professional points of view and propose features. It contains **no
> citations**, and nothing in it should be read as a literature finding.
>
> It is stage one of three, and it is published because stages two and three
> *overrule* it. `EXPERT-AUDIT-twelve-lens.md` audits this panel's output against
> the published research and rejects several of its proposals outright — the
> physician lens calls the sleep-compression detector and movement ledger "the
> highest-risk items in the entire backlog." `DESIGN-RATIONALE.md` is what
> survived, with citations.
>
> Read in that order, this is the *hypothesis*, not the conclusion. No redaction
> was needed; this document contained no identifying detail.

**Panel:** academic researcher · psychologist · sociologist · productivity expert · life coach · fitness trainer · dietician · doctor.
**Implementation specs:** one per recommendation in the internal `specs-expansion/` set, which is not part of this publication.

## The panel's review of the current system

**Consensus strengths:** the behavioral architecture is unusually sound — trustworthy silence, one-tap actions, wins surfaced, decisions batched, noise budgeted. The psychologist and productivity expert had little to add to what exists.

**Consensus gap:** the system manages *logistics* comprehensively but touches the family's *bodies* almost nowhere — movement, food quality, sleep, and health administration are absent or thin. The doctor's framing: "You've automated the calendar; the calendar's job is to serve the humans." Second gap (researcher): the system measures the kids' school outcomes but not *its own* — no efficacy data. Third (sociologist): it optimizes the nuclear household but barely touches the village around it, and household labor remains uncounted even as the system does more of it.

**Panel cautions for everything below:** health features must never become surveillance or scorekeeping (no wearables, no weight, no per-kid competition); food features must be variety-positive, never restrictive; relationship features must nudge, never dashboard. These rules are baked into the individual specs as hard constraints.

## The 25, mapped (input → output → intended impact)

| # | Rec (spec file) | Lens | Input → Output | Impact |
|---|---|---|---|---|
| 1 | Family movement ledger | trainer | calendar + /move → weekly active-days line + low-week nudge | everyone moves; sedentary weeks caught early |
| 2 | Training block proposer | trainer | open calendar slots → 3 concrete workout slots when self-care gap fires | dad's fitness survives the family calendar |
| 3 | Pre-season conditioning ramp | trainer/doctor | /season dates → T-21/T-7 ramp tasks | fewer first-week injuries |
| 4 | Family monthly challenge | trainer/psych | movement counts → cooperative pace line | fun, collective, zero sibling rivalry |
| 5 | Dinner variety scorecard | dietician | AnyList meal plan → 1-line variety check | balanced weeks without diet-policing |
| 6 | Pack-lunch rotation | dietician | lunch cache + PACK_IDEAS → rotating pack suggestion | no PB&J rut; faster mornings |
| 7 | Sports-day fuel line | dietician/trainer | gear match + event time → water + snack-timing | kids fueled for 4pm games |
| 8 | Grocery gap detector | dietician/prod | meal plan vs Pantry list → skip-owned + staples check | no double-buys, no missing tortillas |
| 9 | New-food Friday | dietician/psych | favorites-adjacent recipe monthly → taste-test framing | palate growth without dinner battles |
| 10 | Sleep-compression detector | doctor | next week's late events → schedule-squeeze flag | bedtimes protected before the week locks |
| 11 | Medical document registry | doctor | /med expiry dates → pre-expiry tasks | physicals never block a registration |
| 12 | Illness mode (/sick) | doctor | one command → nags paused, absence email drafted, softer framing | sick days lose their 7am scramble |
| 13 | Heat/AQI alerts | doctor | NWS+AirNow on outdoor days → concrete adjustment line | safe summer practices |
| 14 | Checkup prep journal | doctor | sizes history + /forvisit notes → prep card at recall time | better pediatric visits |
| 15 | One-on-one time nudge | psych | calendar + /dadtime → monthly-ish nudge per kid | relationship deposits don't starve |
| 16 | Process-praise cards | psych | each kid win → ready-to-say process praise | praise that builds persistence |
| 17 | Transition warnings | psych | today's departures → T-30 ping | fewer doorway meltdowns |
| 18 | Worry parking (/worry) | psych/coach | 11pm worry → Sunday-once resurface | sleep now, triage calmly later |
| 19 | Repair nudge (/rough) | psych | rough evening → private next-morning repair script | ruptures get repaired |
| 20 | Family council agenda | sociologist | decisions + wins + calendar → monthly kid-inclusive agenda | kids get voice; logistics get owners |
| 21 | Village ledger (/favor) | sociologist | favor log → monthly reciprocity nudge | the village stays tended |
| 22 | Equity snapshot | sociologist | done-task history → monthly invisible-work count (no names) | coordination labor becomes visible |
| 23 | Errand batching | productivity | errand tasks + open slot → one suggested run | four trips become one |
| 24 | Guided Sunday review | prod/coach | weekly payload → 4-step close-out ritual | the week actually closes |
| 25 | Outcome metrics | researcher | task lifecycle + logs → monthly report card w/ named misses | the system proves (or fixes) itself |

## Suggested build order

**Wave 1 (highest impact, mostly small):** 25 (measure first — the researcher insists baseline precedes intervention), 12, 11, 15, 24, 2.
**Wave 2:** 1, 10, 17, 16, 6, 7.
**Wave 3 (needs AnyList MCP live):** 5, 8, 9.
**Wave 4:** 3, 13 (needs AIRNOW_KEY), 20, 22, 23, 18, 19.
**Wave 5 (nice):** 4, 14, 21.

Sequencing rationale: measurement before new features (researcher); health-admin before health-behavior (doctor — remove friction first); relationship nudges early because they're cheap and compounding (psychologist); everything AnyList-dependent waits for Part B of the install guide.
