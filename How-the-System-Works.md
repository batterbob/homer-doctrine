# How the System Works — HOMER

*HOMER watches deterministically; Hermes investigates and drafts.*

HOMER (Household Operations, Monitoring, Escalation & Reporting) is the
deterministic household application. **Daily Brief** is one of its features and
outputs; **Hermes** is the separately integrated conversational assistant.

*Goals, the design choices that serve them, and the mechanics underneath.
Four pages. Written 2026-07-05. If this document and a newer decision
conflict, the DECISIONS-REGISTER wins.*

> **Note on this public edition.** This document describes a system running in
> one real household. Operator names, children's names, the school district,
> home-network addresses and similar identifying detail have been replaced with
> role descriptions — "the primary operator", "the second operator", "the older
> child". No argument, decision or mechanism has been altered.

---

## 1. Goals

The system exists so that **nothing school- or household-shaped falls
through the cracks, at minimum attention cost, for both parents.** Every
other goal qualifies that one:

1. **Minimum attention.** The scarce resource is not information — it's the
   family's attention. Chattiness and nagging are costs, not features.
2. **Solutions, never bare problems.** Every problem surfaced arrives with
   the smallest next action already attached — a command to tap, a draft to
   approve.
3. **Sticky and enjoyable.** A system that becomes a dreaded chore gets
   abandoned, and an abandoned system catches nothing. It must be clear,
   concise, obvious, and pleasant enough to still be in use next year.
4. **Proactive with approval.** It offers to act — several options where
   sensible — but never acts on its own. A human approves everything that
   sends, spends, or changes.
5. **Kids' wellbeing, not surveillance.** The data flow watches schools so
   the parents don't hover. It must never become a scoreboard or a means of
   policing the kids.
6. **Be needed less over time.** Success is the family carrying less
   invisible load and the kids growing more capable — not engagement going
   up.

## 2. Design choices, and the goals they serve

**Code decides, model narrates** *(serves 1, 4).* All detection —
grades, deadlines, bills, conflicts — is deterministic Python. The AI never
decides what matters or whether to alert; it only phrases what code found,
plus two tightly validated extractors that read email prose. This bounds
what any AI mistake can do: a bad model day produces awkward wording, never
a missed deadline or a phantom alarm. It also means silence can be trusted,
which is the next choice.

**Silence is earned** *(serves 1, 3).* Quiet channels stay quiet when
there is truly nothing — and the system guarantees "truly" by making every
failure path loud: a broken fetch, an unreadable PDF, a stale backup each
announce themselves once, with a suggested fix. The one deliberate
exception: the 6:00 morning brief always sends, so its absence itself
signals breakage. Research basis: clinical alarm-fatigue studies — operators
tune out systems that cry wolf, and one false quiet day costs more trust
than fifty accurate alerts buy.

**Noise is budgeted** *(serves 1, 3).* At most 4 context-switches per day,
a handful of fixed send times rather than a drizzle of pings, display caps
(8 tasks shown, "+N more"), decisions batched to Sunday instead of
ambushing weekdays, and a standing rule: no new notification slot without
naming the slot it replaces. Field research on notification batching shows
a few predictable daily batches beat both continuous alerts and none.

**No flag without its smallest next action** *(serves 2).* Every alert ends
in the one concrete thing to do — "/done t3f9a", "ask Hermes to draft the
note", a pre-written draft waiting in Fastmail. This is the system's
highest-value rule: implementation-intention research (94 studies) shows
pairing a cue with a specific action roughly doubles follow-through. The
gap between knowing and doing is where family logistics rot.

**Wins are described, never counted** *(serves 3, 5).* The briefs surface
good news — a cleared zero, a grade recovery, a finished to-do — because
negativity-bias research says a brief that only carries problems becomes a
punishment device you stop opening. But wins are named, never scored: no
tallies, no streak numbers, no leaderboards. Reward research shows
counting turns care into point-chasing; a specific sentence ("he cleared
the essay zero") affirms competence without gamifying the family. Streak
breaks are silent by design — fresh start, never shame.

**Knowledge, not surveillance** *(serves 5).* Adolescent-development
research (Stattin & Kerr) is blunt: parental knowledge protects kids, but
it comes from kids disclosing, not parents tracking — and feeling policed
makes things worse. So: verify before confronting, never raise a school
flag in the pickup window (the system holds those until evening), praise
recoveries specifically, and the roadmap points toward the older child
getting his own weekly summary — his tool, his data, his choice what to
share.

**Two operators, not one operator and one reader** *(serves the core goal).*
The second operator gets their own Sunday digest that leads with what's
already handled, and full access to Hermes — capability, not just reports.
If the primary operator goes quiet for days during term, the system notices
and offers the second operator the reins. The household-labor research
point: a tool only one partner can drive digitizes the imbalance it was
meant to fix.

**Protect sleep and ritual** *(serves 3).* Daily briefs are terse wire
copy; the Sunday planner is where warmth lives. The day ends at 20:30 on a
note of closure — a win or "you're clear," never an open problem — and
after 21:00 the system declines to open the task list at all ("that'll
keep you up — parked for the 6am brief"). Pre-sleep cognitive-arousal
research: reviewing tomorrow's obligations at bedtime measurably wrecks
sleep.

**Measure, then decide; when in doubt, do less** *(serves 6).* The system
logs its own performance privately — never as a family-facing score — and
compiles it quarterly so feature decisions run on evidence instead of
enthusiasm. New detectors serve a two-week probation (quarantine) before
they may interrupt anyone. The feature budget is replace-don't-add, because
abandonment research says upkeep burden, not missed alerts, is how personal
systems die.

## 3. Mechanics

**Two components on the home NAS.** The **Daily Brief pipeline** is a
Python/Docker program run on a schedule by cron. It fetches the gradebook portal
(grades, attendance), the LMS (assignments, submissions), Fastmail (email
and PDF attachments), the family calendar, and school lunch menus;
compares each fetch against the last known state; and turns differences
into messages. **Hermes** is a separate AI agent with its own Telegram bot:
the on-demand layer that investigates ("why did the Math grade drop?"),
drafts emails (to Fastmail Drafts only — it cannot send), and translates
plain English into exact pipeline commands.

**The day.** 6:00 — morning brief, always: flags with next actions, wins,
today's gear per kid, the to-do nag list, bills, school notes, email
digest. 16:00 — afternoon check, silent unless school changed or homework
is due tomorrow (the dinner-table list); kid-behavior flags detected here
are held until evening. 20:30 — close-of-day to both parents: submission
status, tomorrow's logistics, today's wins, decisions pending, ending on
closure. Every 15 minutes, invisibly, a commands run answers /done,
/snooze, /todo, /sick and the rest, and fires registration-countdown
alerts. Sunday 18:00 — the weekly planner, then the second operator's
≤15-line digest; 19:30 — the guided family review; nothing after 21:00.

**State and memory.** Everything the system knows lives in one JSON state
file, written by exactly one run at a time (a lock enforces it). Tasks nag
every morning until acted on, then count as wins; ignored tasks get one
final expiry notice, never silent death. If a fetch fails, the old data is
carried forward as the baseline and flagged — a broken login can never
masquerade as a quiet day, and recovery produces one honest delta rather
than a noise flood. Every run appends a line to a log; the log feeds the
metrics.

**The handoff.** When a flag has a draftable action, the pipeline drops a
small job file in a shared folder; Hermes polls it, writes the draft into
Fastmail Drafts in the primary operator's voice, and pings that it's ready.
The boundary is structural: the job schema cannot express "send," Hermes'
email access excludes sending, and the pipeline never reads anything Hermes
produces — it only watches file ages and flags jobs that sit unprocessed.

**The visual layer.** Tasks project into Todoist as four lists — Today
(max 8, curated: overdue, then escalated, then soonest), This Week,
Waiting On, Later. Checking an item off in Todoist counts as /done.
Unfinished Today items roll quietly; on the third roll the system forces a
one-tap decision: do today, snooze, delegate, or kill. The pipeline
remains the single source of truth; Todoist is a projection.

**The feedback loops.** Continuous: every run logs messages sent, flags
raised, flags acted on, and per-detector accuracy. Monthly: one private
alarm-health line (are you acting on fewer flags? — the early warning of
tune-out). Quarterly, first Sundays of Oct/Jan/Apr/Jul: the pipeline
compiles the quarter's data and Hermes walks the primary operator through it
one metric at a time — plain language, a refresher of what each thing is,
the concrete result of each recommendation — with every decision made as
approve/deny/defer/adjust and recorded to the shared journal. Hermes
recommends and records; only a human changes configuration.

**Shared memory across the AIs.** A NAS folder bridges the three systems:
Cowork writes memory.md (project state and decisions), Hermes appends a
one-line journal of notable actions, and both read the operator-profile and
writing-style files that only the primary operator edits. No secrets ever
enter these files — they're injected into AI prompts on both sides.

**Safety rails, stated once.** Every detector has an env kill switch — no
rebuild needed. Money never moves automatically. Nothing emails relatives,
teachers, or schools without a human pressing send. Kids' raw data never
enters Docker images or leaves the NAS unencrypted; nightly encrypted
backups go offsite, and the morning brief checks they happened. And the
ten hard rules above every build prompt ensure that no future feature —
or future AI — quietly unwinds any of this.

*Deeper reading: USER-GUIDE (features and commands) · DESIGN-RATIONALE
(the research, with citations) · DECISIONS-REGISTER (every ruling and why).*
