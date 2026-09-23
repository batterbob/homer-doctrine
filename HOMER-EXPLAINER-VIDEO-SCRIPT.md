# HOMER — explainer video script (4–7 min)

**Purpose:** explain to a non-engineer (or a future household member) what HOMER is,
what it does each day, and why it is built the way it is.
**Format:** narration + on-screen visuals, 8 scenes, ~5.5 min at a calm pace (~950 words).
**Hand this file to Sol to produce the visuals.**

**PRIVACY RULE FOR WHOEVER BUILDS THE VISUALS:** no real child names, school names,
teacher names, grades, addresses, or message contents. Use placeholders
("Kid A", "School", "Math 7"). Any screenshot must be mocked, never a real brief.

---

## SCENE 1 — The problem (0:00–0:40)

**VISUAL:** A morning kitchen. Six phone notifications stack up: a school portal
email, a grade alert, a "picture day tomorrow" note, a permission slip, a bill, a
calendar invite. They pile past the edge of the screen.

**NARRATION:**
"Every family runs on information that arrives in the worst possible way — a dozen
sources, none of them talking to each other, all of them at the wrong moment. The
school portal has grades. Email has the permission slip. The calendar has the early
dismissal nobody noticed. Nothing is missing, exactly. It's just scattered across
fifteen places, and the cost of checking them all is a person's attention, every day.

HOMER is the thing that checks them, so you don't have to."

---

## SCENE 2 — What HOMER is (0:40–1:20)

**VISUAL:** The acronym assembles: **H**ousehold **O**perations, **M**onitoring,
**E**scalation & **R**eporting. Then it splits into two panels — left: a watchtower
labeled "the pipeline"; right: a messenger labeled "the assistant."

**NARRATION:**
"HOMER has two halves, and the split is the whole design.

The first half is a pipeline. It runs on a schedule, it fetches from every source,
and it decides — in ordinary, testable code — what actually matters today.

The second half is an assistant you can talk to. It answers questions, drafts
replies, looks things up.

The rule that keeps this trustworthy: **code decides, the model narrates.** Every
detection — every 'this grade dropped,' every 'this needs a signature by Friday' —
is deterministic code that can be tested and proven. The language model's only job
is to say it in a human sentence. It never decides what's important. That means the
system can't hallucinate a problem, and it can't hallucinate one away."

---

## SCENE 3 — The daily rhythm (1:20–2:20)

**VISUAL:** A 24-hour clock. A marker lands at 6:00 AM, glowing. Small ticks every
five minutes around the rest of the dial. A short evening marker.

**NARRATION:**
"Here's a day.

At six in the morning, one brief arrives. Today's calendar, what the kids need to
bring, anything that needs a decision, and anything that changed since yesterday.
That brief always sends — even on a quiet day it says 'quiet day.' Because silence
from a system that's supposed to be watching is indistinguishable from a system
that's broken.

Every five minutes after that, HOMER checks for anything you sent back — a reply, a
button tap, a photo of a form that came home in a backpack.

And then, mostly, it says nothing. Every other channel stays quiet unless there is
something true and useful to say. That's a rule with a name: **silence is earned.**
A system that pings you to prove it's alive has already cost you more than it saved."

---

## SCENE 4 — Where the information comes from (2:20–3:10)

**VISUAL:** Sources flow into a funnel: school portal (grades, attendance,
documents), school messaging, school news, email, calendars, task list, photos.
Each source has a small toggle switch beside it, all currently "on."

**NARRATION:**
"The sources: the school grade portal, school announcements, the school news feed,
email, the family calendars, the shared task list — and photos, because half of
school life still arrives as a piece of paper in a backpack. Snap it, and HOMER
reads it.

Each source is sealed off from the others. It fetches its own data, translates it
into one shared format, and nothing downstream ever knows or cares where it came
from. If one source breaks — a password expires, a site changes — that source
reports itself broken, keeps yesterday's data as a baseline, and **the rest of the
brief still arrives.** One failure never takes the morning down.

And every source has an off switch, so any of them can be disabled in seconds
without touching code."

---

## SCENE 5 — What you actually see (3:10–4:00)

**VISUAL:** A mocked phone screen: a short morning brief. A few lines, each with a
small action button — "Done," "Snooze 3 days," "Remind me." Then a web page mock:
the same information, a bit richer.

**NARRATION:**
"What reaches you is deliberately small. A brief you can read standing up. Each item
that needs something carries exactly one next step — a button, a draft, a task.
That's another rule: **no problem without the smallest next action.** Telling you
something is wrong and leaving you to figure out the response is just moving the
work.

There's also a page you can open for the fuller picture, and an assistant you can
ask follow-up questions.

What you will never see: a score, a streak, a leaderboard, a number ranking how the
week went. Wins get described — 'she turned in every assignment this week' — never
counted. The moment a household system starts scoring people, it stops being a help
and starts being a judge."

---

## SCENE 6 — The guardrails (4:00–4:50)

**VISUAL:** Four cards flip up: "Never acts alone." "Attention is budgeted."
"Knowledge, not surveillance." "The goal is to be needed less."

**NARRATION:**
"Four rules do most of the safety work.

HOMER never acts on its own. It drafts, it recommends, it prepares — a human
approves before anything is sent or changed. Nothing goes out on its own authority.

Attention is budgeted — a hard ceiling of about four interruptions a day. A new
notification can only be added by naming the one it replaces.

With the kids, it's knowledge, not surveillance. It surfaces what a parent would
reasonably know — a missing assignment, an absence — not a feed of everything a
child does.

And the goal is to be needed *less* over time. If a feature makes the household
depend on it more, that's a failure, not a win."

---

## SCENE 7 — How it's built (4:50–5:40)

**VISUAL:** Four labeled figures around a shared document: a planner, a builder, an
independent auditor (in a different color, standing apart), and a merger. An arrow
loops: build → audit → revise → audit → merge.

**NARRATION:**
"One more thing, because it explains the reliability.

HOMER is built by several AI agents with strictly separated jobs. One plans. One
writes code. One — deliberately a *different* agent, that never sees itself as the
author — tries to break it. A fourth is the only one allowed to merge anything.

Nothing lands on a single agent's say-so. The auditor's job is to find the case
where the new code is wrong, and it routinely does: a stale button that could undo
the wrong task, a timer that quietly counted from the wrong day. Those got caught
before they ever ran in the house.

Every one of those findings is written down in a shared ledger, with the evidence.
The system's own history is auditable, not just its code."

---

## SCENE 8 — Close (5:40–6:10)

**VISUAL:** Back to the kitchen. One phone, one short brief. The other notifications
are gone. Someone reads it, taps one button, puts the phone down.

**NARRATION:**
"So that's HOMER. It watches the things a household has to watch, decides in code
what actually matters, says it in one short message at six in the morning, and then
gets out of the way.

Not a dashboard. Not another app to check. One brief, one button, and a quiet day
when the day is quiet."

**END CARD:** HOMER — Household Operations, Monitoring, Escalation & Reporting.
*HOMER watches. Hermes investigates and drafts.*

---

## PRODUCTION NOTES (for Sol)

- **Tone:** calm, plain, unhurried. No hype, no "AI-powered," no product-launch voice.
  This explains a household tool to a person, not a platform to investors.
- **Pace:** ~150 words/min. Full script ≈ 950 words ≈ 6:20 with pauses. To reach 4:00,
  cut Scene 7 (how it's built) and tighten Scene 4 — the family-facing story survives.
- **Visual style:** simple, flat, uncluttered. The visual argument is *less* — every
  scene should have fewer elements than the last, ending on one phone and one message.
- **The one motif worth repeating:** the notification pile from Scene 1 shrinking each
  time it reappears, until it's a single line in Scene 8.
- **Do not visualize** real data, real names, or a real brief. Mock everything.
