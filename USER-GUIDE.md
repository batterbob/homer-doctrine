# HOMER — User Guide

### *HOMER watches deterministically; Hermes investigates and drafts.*

**HOMER** — Household Operations, Monitoring, Escalation & Reporting — is the
deterministic household application. **Daily Brief** is a HOMER feature and
output. **Hermes** is the conversational assistant integrated with HOMER. The
governance and privileged-operations boundaries remain separate from both.

> **Note on this public edition.** This guide was written for the two adults who
> actually run the system. Operator names, children's names, the school district
> and similar identifying detail have been replaced with role descriptions —
> "the primary operator", "the second operator". No feature, command or design
> decision has been altered.

## 1. What this system is

Pipeline watches and nags deterministically. Hermes investigates and drafts on demand. Cowork architects. That one sentence is the whole system. The pipeline never asks a model whether something is due; it computes it. Hermes never runs a scheduled monitor; it answers, investigates, and drafts when you ask.

## 2. Design principles (your memory anchors)

1. **Code decides, model narrates.** Detection is deterministic Python. The model only phrases things and runs two narrow, add-only extractors.
1. **Silence is earned.** A quiet channel means nothing was true, not that something broke — except the 6:00 morning brief, which always sends; silence there is breakage.
1. **No problem without the smallest next action.** Every flag carries one concrete next step.
1. **Noise is budgeted; attention is scarce.** Target ≤4 context-switches/day. Batch, cap, and replace — never just add.
1. **Wins are described, never counted.** You’ll hear “he turned in the essay he was stuck on,” never a points tally.
1. **Knowledge, not surveillance — especially with kids.** No flags at pickup; verify before confronting; move kids toward self-monitoring; praise recoveries.
1. **Two operators, not one operator + one reader.** The second operator has real access, not just a digest.
1. **Protect sleep and ritual.** Warm Sunday, terse weekdays, a 21:00 hard stop, and every day ends on closure.
1. **The goal is to be needed less.** Features that stop earning their place get cut.
1. **When in doubt, do less.**

## 3. Your day & week

|When           |What                                                                                                   |Silent when…                                            |
|---------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
|6:00           |Morning brief                                                                                          |Never silent (silence = breakage)                       |
|16:00          |Afternoon delta + homework-due-tomorrow                                                                |Silent when nothing changed                             |
|20:30          |Close-of-day: submission status → tomorrow logistics → today’s wins → decisions count. Ends on closure.|Deterministic, always brief; no open problems at bedtime|
|Sun 18:00      |Weekly planner + the second operator’s ≤15-line digest                                                 |—                                                       |
|Sun 19:30–19:55|Guided review (4 steps)                                                                                |—                                                       |
|every 15 min   |Commands + watch alerts (Telegram)                                                                     |Silent unless you act or an alert fires                 |

Sunday sequence: 18:00 planner → dinner → 19:30 review → 20:30 close-of-day → nothing after 21:00. Saturday’s close-of-day slot is replaced by Hermes’ decision-research.

## 4. Features A–Z

Action mail (permission slips incl. PDF→deadline tasks, party invites→RSVP+gift, registration→suggested /watch, the youth-sports app→proposed event+.ics, the cafeteria portal low balance→task) · assignment milestones (T-14/-7/-3) · bill tracker (nags until 3 days past due) · calendar conflict detection (TRANSITION_MINUTES=30) · decision queue (/decide → Sunday, never daily) · draft handoff (pipeline queues a job; Hermes drafts to Fastmail DRAFTS; you approve/send) · gear-of-the-day (incl. sports-day fuel) · grade archive (monthly + June year-in-review) · grandparent update draft (first Sunday monthly, draft only) · inline buttons ([✓ Done] [Snooze 3d]; typed commands still work) · kid size registry + seasonal audits (Mar/Aug/Nov) · lunch buy/pack lines · /med medical registry (physicals/immunizations, MED_LEAD_DAYS=21) · recalls (medical/dental, RECALL_LEAD_DAYS=21) · school notes (per-kid, prefiltered) · self-care gap (one gentle weekly line) · /sick illness mode (pauses a kid’s nags, drafts absence email, softens framing, auto-expires) · social reciprocity ledger (/social) · task engine (nag until /done, /snooze, 15-day expiry with one final flag, 8/12 caps + “+N more”) · /todo manual add · Todoist projection (below) · vacation mode + “while you were out” digest · watches (7d/1d/1h/10m/open) · wins/cleared-zeros surfacing · silent streak breaks.

**Todoist four-list system:** ⚡Today (max 8) · 📅This Week (≤7d) · ⏳Waiting On (Sunday-only) · 🗄️Later. Topic emoji: 🎒school 🏠house 💰money 🚜farm 👨‍👩‍👦family. **Rollover tripwire:** tasks roll silently; the *third* rollover raises a decision flag (do today / snooze / delegate / kill). AnyList stays groceries/recipes only.

**Interactive suggestions & flag-watch (Phases 8–14, 2026-07-19):**

- **The star means "needs action."** Star (flag) any email in Fastmail and HOMER turns it into a `flag_followup` task within ~5 minutes. Un-star it (or let it age out of the window) and the task self-clears silently. **Best practice: unread = "not yet seen"; the star = "needs action."** Never re-mark a read email as unread to remind yourself — star it instead. HOMER never marks anything unread and never sets a star; the only mailbox write it ever makes is *removing* a star you've handled (and only after a 14-day probation you approve by setting `FLAG_CLEAR=1`).
- **Forwards from your co-operator are delegations.** A "Fwd:" from either operator's personal address (set in `OPERATOR_EMAILS`) becomes an action candidate even if it hits no other rule — the fix for the day a forwarded camp email produced nothing.
- **The all-mail sweep** quietly reads ordinary inbox mail the rules miss and, once out of its 14-day probation, turns a genuine "needs a reply/decision/payment/signup" into a task.
- **Pending suggestions & "I'm ready."** Non-urgent items (events more than ~2 days out, gear ideas, planning gaps, swept actions) don't interrupt — they wait behind ONE morning footer line: *"N suggestions waiting — tell Hermes 'I'm ready' to work through them."* You approve or dismiss them by talking to **Hermes**, one at a time. Urgent items (today/tomorrow) still land inline with their .ics exactly as before.
- **Planning gaps.** A calendar event you made whose title says "TBD" / "time TBD" / "??" becomes one "Plan this with Hermes" suggestion (once per event, ever).

**Operator mental model — who you talk to:** **You talk to Hermes; the pipeline only hears yes/no/later about its own output.** HOMER (the pipeline) is the push half — it sends buttons and the morning brief and handles taps on *its own* buttons. Anything interactive — planning, approving suggestions, asking questions — is Hermes' job. If you send the pipeline plain text, it replies once: *"I'm the push half — for anything interactive, message Hermes."*

## 5. Commands reference

|Command                 |Does                            |
|------------------------|--------------------------------|
|/help                   |Command list                    |
|/tasks /done /snooze    |Task engine                     |
|/todo <text> [due]      |Manual task add                 |
|/sick <kid> [off]       |Illness mode                    |
|/med                    |Medical document registry       |
|/watch /watches /unwatch|Registration watches            |
|/recall /recalls        |Medical/dental recalls          |
|/size /sizes            |Kid size registry               |
|/decide /decided        |Decision queue (surfaces Sunday)|
|/social                 |Reciprocity ledger              |
|/vacation               |Vacation mode + WYWO digest     |

## 6. The quarterly review

First Sundays of Oct/Jan/Apr/Jul the pipeline compiles a quarterly JSON. The planner tells you: “Quarterly review ready — tell Hermes ‘run the quarterly review’.” Hermes walks you through it **one metric at a time**: recap → what it means → its recommendation. For each you choose **approve / deny / defer / adjust** (adjust = a knob change, e.g. TRANSITION_MINUTES 30→15). Never batched; always a do-nothing option. Hermes records the decisions to its journal (Cowork mirrors to memory.md). Hermes recommends and records; it never changes config itself.

## 7. Metrics & data

**What’s collected (private to the system, never family-facing, never a score):** context-switches sent, flags raised, flags acted on, per-detector fire/true-positive (PPV), per-feature last-used dates. **Cadence:** captured per run; a monthly alarm-health line; compiled quarterly. **Where it lives:** briefs/log.jsonl and briefs/quarterly-YYYY-QN.json. **Privacy stance:** measurement exists to keep the system honest about its own noise — it is never shown to the family and never turned into a number about a person.

## 8. Design decisions to remember (so you don’t re-litigate)

- **Watchlist (kill criteria at Sept/quarterly):** calendar conflicts, school notes, social ledger, standup (already fixed by consolidation), Hermes needs-a-reply (already on-demand/weekly). Nothing has been uninstalled.
- **DEFER (revisit 2026-09-13):** one-on-one nudge (#15), process-praise cards (#16), transition warnings (#17), worry parking (#18), repair nudge (#19), errand batching (#23, needs Todoist), training-block proposer (#2), family-council agenda (#20).
- **REJECTED (one-line why):** movement ledger (#1, surveillance-y), conditioning ramp (#3), monthly challenge (#4), dinner-variety scorecard (#5, counts a win), pack-lunch rotation (#6), grocery gap detector (#8), sleep-compression detector (#10), heat/AQI alerts (#13, noise), checkup prep journal (#14), village ledger (#21), equity snapshot (#22, replaced by the quarterly reflection prompt).
- **RESHAPED:** sports-day fuel (#7) → GEAR_RULES edit; new-food Friday (#9) → Hermes Playbook monthly pattern.
- **AnyList vs Todoist:** Todoist projects the *task* system; AnyList is groceries/recipes only (Mealie is the fallback if AnyList breaks >1×/quarter).
- **Why 20:30 replaced two sends:** one close-of-day beats an evening ping plus a standup — fewer switches, and it ends on closure to protect sleep.
- **Why streaks break silently:** a broadcast streak-loss demotivates; the break is logged, not announced.
- **Why wins aren’t counted:** counting converts a win into a score and erodes the thing it measures.
- **Why the second operator has access, not a digest:** they’re an operator, not an audience.

## 9. When something looks wrong

- **Quiet by design:** afternoon/close-of-day/commands are silent when nothing is true. Only a silent *morning* brief is a problem.
- **Kill switches:** env flags (SCHOOL_NOTES=0, TODOIST_ENABLED=0, ACTION_MAIL, SUBMISSION_CHECK, STANDUP, ATTENDANCE_ALERTS, etc.), Todoist projection off, quarantine flags to log-only. New ones: PICKUP_WINDOW, MED_LEAD_DAYS, TASK_ESCALATE_DAYS, OPERATOR_LIVENESS_DAYS.
- **Logs:** briefs/log.jsonl (runs), briefs/state.json (state), quarterly JSON.
- **Who to ask:** Hermes for “what happened / draft me a reply”; Cowork for “build/change it”; RUNBOOK for “it’s down.”

## 10. Care & feeding

Monthly: read the alarm-health line; the grandparent draft lands (approve it); Hermes shows its work once; the journal self-compacts. Quarterly: run the guided review; answer the cognitive-labor reflection prompt; AnyList health check. Annually: June year-in-review + retro; August pre-school-year checklist; September 13 audit.

## 11. Glossary

**PPV** positive predictive value (true positives ÷ fires). **Context-switch** one delivered message = one switch (budget ≤4/day). **Alarm health** flags raised vs flags acted. **PIM** personal information management. **MCP** Model Context Protocol (tool bridge). **JMAP** Fastmail’s email API. **Delta** what changed since the last run. **Baseline** the last known-good state a delta compares against. **Carry-forward** reusing the prior slice when a fetch fails, so a gap doesn’t corrupt state. **Watch** a registration you’re waiting to open (7d/1d/1h/10m/open). **Recall** a medical/dental follow-up due. **Handoff job** a job-<id>.json the pipeline leaves for Hermes to draft. **Projection** pushing tasks into Todoist’s four lists. **Rollover tripwire** the third silent rollover raises a decision flag. **Kill switch** an env flag that turns a feature off. **Wire copy** the exact text a message sends. **Hard rules** non-negotiable design rules every change is checked against; called "invariants" in earlier project docs.
