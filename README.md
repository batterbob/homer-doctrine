# HOMER — design doctrine

*A deterministic household system, and the reasoning behind it.*

HOMER (Household Operations, Monitoring, Escalation & Reporting) watches a real
family's school and household data — grades, assignments, email, calendars,
documents, lunch accounts — and delivers a small number of messages a day. It has
run unattended in one household since mid-2026.

This repository is **not the code.** It is the thinking: the goals, the research
the design was checked against, the decisions that were made and rejected, and
the rules that keep a system like this from becoming the thing it was meant to
fix. It is published in the spirit of *"how we built ours"* — a reference, not a
product. There is no support promise, no installer, and no intent to run this as
a service for anyone else.

---

## The one-paragraph version

Household information arrives scattered across a dozen sources at the worst
possible moments, and the obvious fix — a system that watches everything and
tells you about it — reliably makes things worse. Alert-heavy systems get tuned
out, then dreaded, then abandoned. HOMER's answer is that **code decides and the
model narrates**: all detection is deterministic Python, the LLM only phrases
what code already found, and silence is a claim the system has to earn by making
every failure loud. Everything else follows from budgeting attention rather than
maximizing coverage.

## Start here

1. **[How-the-System-Works.md](How-the-System-Works.md)** — goals, the design choices that serve them, and the mechanics underneath. Four pages. If you read one document, read this one.
2. **[DESIGN-RATIONALE.md](DESIGN-RATIONALE.md)** — 16 design choices, each with the alternative that was rejected and the peer-reviewed evidence behind it.
3. **[SOURCE-CONTRACT.md](SOURCE-CONTRACT.md)** — the single architectural pattern every data source obeys. One page, and the most reusable idea here.

## The research

The unusual part of this project is that the design was not argued from
intuition, and the research happened in three stages that *disagree with each
other*. Read them in order:

| Stage | Document | What it is |
|---|---|---|
| 1 | **[CROSS-FUNCTIONAL-REVIEW-25-recommendations.md](CROSS-FUNCTIONAL-REVIEW-25-recommendations.md)** | A panel of eight expert personas — psychologist, sociologist, doctor, dietician, productivity expert, life coach, fitness trainer, academic — proposes 25 features and sets hard ethical constraints. **Unsourced.** No citations. This is the hypothesis. |
| 2 | **[EXPERT-AUDIT-twelve-lens.md](EXPERT-AUDIT-twelve-lens.md)** | Twelve expert lenses audit that panel **against the published literature** — and reject much of it. The physician lens calls two of the panel's own proposals "the highest-risk items in the entire backlog." Real citations, effect sizes, and honest flagging of its own weak evidence. |
| 3 | **[DESIGN-RATIONALE.md](DESIGN-RATIONALE.md)** | What survived, distilled into 16 cited choices with verified DOIs, plus a section on the limits of the evidence. |

Stage 2 overruling stage 1 is the point. A review that tells you not to build
your own backlog is worth more than one that agrees with you.

Also here: **[ASSISTANT-LANDSCAPE-RESEARCH-summary.md](ASSISTANT-LANDSCAPE-RESEARCH-summary.md)**, a survey of the surrounding
product and academic landscape, and **[STUDY-CROSS-SOURCE-MEMORY.md](STUDY-CROSS-SOURCE-MEMORY.md)**, a worked
engineering study on where deterministic code must own a judgement and where a
language model may assist.

## Decisions and governance

- **[DECISIONS-REGISTER.md](DECISIONS-REGISTER.md)** — the master record: what was installed, deferred, rejected, and why. Includes the cost decisions with real numbers, the model-tier reasoning, recorded mistakes, and gates that were deviated from and said so.
- **[GOVERNANCE-CHARTER.md](GOVERNANCE-CHARTER.md)** — the system-wide charter and non-negotiables.
- **[REPOSITORY-MAP.md](REPOSITORY-MAP.md)** — how the repositories are separated, and why identity is kept distinct from naming.

## For a general audience

- **[HOMER-EXPLAINER-VIDEO-SCRIPT.md](HOMER-EXPLAINER-VIDEO-SCRIPT.md)** — an eight-scene narrated explainer.
- **[HOMER-VIDEO-SCRIPT-90s.md](HOMER-VIDEO-SCRIPT-90s.md)** and **[HOMER-VIDEO-90s-IMAGE-PROMPTS.md](HOMER-VIDEO-90s-IMAGE-PROMPTS.md)** — a 90-second version with illustration prompts.
- **[USER-GUIDE.md](USER-GUIDE.md)** — the everyday feature and command reference, as written for the two adults who actually run it.

---

## The ten principles

These are the whole design, compressed. Everything in this repository is either
an argument for one of them or a consequence of one.

1. **Code decides, model narrates.** All detection is deterministic. The LLM phrases; it never detects, decides, or acts.
2. **Silence is earned.** A quiet channel means nothing was true — guaranteed by making every failure path loud, with one exception: the morning brief always sends, so its absence signals breakage.
3. **No problem without the smallest next action.** Every flag ends in one concrete thing to do.
4. **Noise is budgeted; attention is scarce.** At most four context-switches a day. Batch, cap, and replace — never just add.
5. **Wins are described, never counted.** No scores, streaks, or leaderboards. Counting turns care into point-chasing.
6. **Knowledge, not surveillance — especially with kids.** Parental knowledge protects; tracking backfires. Move toward the kids monitoring themselves.
7. **Two operators, not one operator and one reader.** Both adults get capability, not one getting a report.
8. **Protect sleep and ritual.** Terse weekdays, a warm Sunday, a hard evening stop, and every day ends on closure.
9. **The goal is to be needed less.** Success is declining dependence, not rising engagement.
10. **When in doubt, do less.**

---

## On privacy, and what was changed for publication

HOMER runs on one family's real data, including two children's. The project has
a standing rule that no child's name, school, teacher, grade, or surname may
appear in any public artifact, and that the live repositories are never made
public — any release is a clean-room export.

These documents are that export. Every one of them carries a note stating what
was changed. The convention throughout:

- The two adults are **"the operator"** and **"the second operator"** / **"the co-operator"**.
- The children are **"the older child"** and **"the younger child"**.
- One teacher's name became **"the teacher"**.
- Home-network addressing, VPN device names, repository accounts, filesystem
  paths, and the household's own domain were removed or replaced with
  placeholders.
- One document originally recorded the dates the house would be empty. It does
  not now.
- **School-specific vendors are described by role, not name** — "the gradebook
  portal", "the LMS", "the school-comms portal", "the cafeteria portal", "the
  lunch-menu service", "the youth-sports app". Any one of them is unremarkable;
  the particular *combination* would narrow the school district, and from there
  the household. Module names and identifiers derived from those vendors were
  genericized with them, so a few code references name a role rather than the
  real symbol. General-purpose tools any household might use are named
  normally — they narrow nothing.

Arguments, decisions, dates, dollar figures, rejected options and recorded
mistakes are unchanged. Where a passage's *reasoning* depended on a specific
detail, the reasoning was kept and the detail described generically.

## What is deliberately not here

- **The code.** A code release is a separate decision that depends on making the
  data sources pluggable first; today's integration layer is bespoke to one
  school district and one stack.
- **Operational runbooks, deployment steps, and infrastructure configuration.**
- **Anything describing the household's people.** The system builds an internal
  model of the family to do its job. None of it is in this repository, and none
  of it ever will be.
- **Exploitation detail for known-open weaknesses.** The decisions register
  records accepted security trade-offs as decisions; it does not publish the
  specifics that would make them actionable.

## Status

This is a snapshot of a working system's documentation, not a maintained
project. The ideas are offered freely; the system itself is one household's and
stays that way.

## License

The documents in this repository are licensed under
[Creative Commons Attribution 4.0 International](LICENSE) (CC BY 4.0). You may
share and adapt them, including commercially, with attribution.

Suggested attribution: *"HOMER design doctrine, CC BY 4.0."*

The license covers this documentation only. No source code is published here —
see **What is deliberately not here**, above.
