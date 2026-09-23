# CLAUDE.md — HOMER (system charter)

**This repo is the documentation & decisions hub for HOMER. It is not product
code. The bounded `coordination/` exception contains only the operational
lease/event/publisher mechanism required to protect this repo's ledger.
Read this before editing anything here, and open the canonical docs it points
to when the task needs them.**

HOMER — **H**ousehold **O**perations, **M**onitoring, **E**scalation &
**R**eporting — watches a real family's school and household data and delivers
to Telegram. It runs unattended, in a family's life, and its failures are silent
by nature. That is why the invariants below are rules, not preferences.

---

## Repository and checkout map

- **HOMER application** — canonical GitHub repository `<org>/homer-core`;
  current NAS compatibility checkout `dailybrief/`. This is the deterministic
  household application and owns the Daily Brief feature/output. All detection
  is Python; the one LLM touchpoint only narrates an already-computed payload.
  Its own `CLAUDE.md` is canonical for application work.
- **`hermes-agent/`** — **Hermes**, the LLM agent half: a local deployment of
  the **open-source Hermes Agent** (Nous Research, MIT), vendored at upstream tag
  `v2026.7.20` — **not pristine: five local commits sit on top**, and they are
  the safety-critical ones (a `secret_redaction` plugin, two gated-create
  fixes found by a live smoke test, and the reverse-handoff emitter gaining
  the `undo` verb). Verify with `git log v2026.7.20..HEAD`; this file
  claimed "no local commits" until 2026-07-30, which would have sent a
  reviewer past the only agent-side code HOMER actually wrote. Drafts
  and recommends; never acts autonomously. Your
  customization to its *code* is minimal (upstream + a one-line dependency add);
  the rest of your Hermes setup is runtime config OUTSIDE this clone
  (`config.yaml`, `.env`, `secrets/` at the `hermes/` root — secrets, never for
  GitHub). It is **not** mirrored to GitHub by design: upstream already is, and
  the whole clone ships nightly to Storj like everything under
  `<nas-root>/`. So this is a local git checkout, not a repo we push.
- **HOMER governance** — canonical GitHub repository
  `<org>/homer-governance` (this repo); current NAS compatibility checkout
  `homer/`. It owns the charter, decisions, audits, runbooks, prompts, and
  coordination mechanism.
- **HOMER Ops** — `<org>/homer-ops`, checked out separately at
  `<nas-root>/homer-ops/`. It is the privileged infrastructure boundary.

`REPOSITORY-MAP.md` is canonical for repository names, immutable identities,
logical coordination aliases, and compatibility paths. **Hestia** is a
historical codename only; do not use it for a current component.

`CLAUDE.md` is a per-repo file: Claude Code loads it by repo/cwd, and a subagent
inherits **only** the one file for the repo it is launched in. So shared
invariants live here canonically **and** are copied into each code repo's
`CLAUDE.md` where agents need them (e.g. the SOURCE CONTRACT is byte-for-byte in
`dailybrief/CLAUDE.md`, canonical in `SOURCE-CONTRACT.md`). If a copy and its
canonical disagree, the canonical here wins and the copy is the bug.

`<nas-root>/` itself is **not** a repo — it is Hermes' runtime home
(state, sessions, secrets, cron) and the backup root. Do not put shared docs or
a `CLAUDE.md` there.

---

## System-wide non-negotiables (canonical: `00-READ-FIRST-invariants.md`)

1. **Code decides, model narrates.** LLM output may only narrate or emit
   validated add-only JSON — never choose quiet/deliver, delete anything, or
   drive control flow. A code-validated fold (narration clusters, Phase 15) is
   narration, not deletion — conservation and provenance are enforced in code.
2. **Silence is earned.** Every failure path emits exactly one flag with a
   concrete next action; every source has a kill-switch env knob.
3. **Never act autonomously.** Money never moves; nothing is auto-sent to third
   parties (school, relatives, other parents). Hermes drafts, a human approves.
4. **Single state writer; wins are described, not counted; attention is
   budgeted** (≤4 context-switches/day — batch, cap, replace-don't-add).

The full ten principles, the eight hard rules, and the SOURCE CONTRACT are in
`00-READ-FIRST-invariants.md`, `SOURCE-CONTRACT.md`, and `dailybrief/CLAUDE.md`.
Read `DECISIONS-REGISTER.md` before proposing anything — the rejected list was
decided deliberately.

---

## Where the canonical docs live (this repo)

- `00-READ-FIRST-invariants.md` — the implementing-model invariants.
- `SOURCE-CONTRACT.md` — the data-source contract (canonical; copied into
  `dailybrief/CLAUDE.md`).
- `DECISIONS-REGISTER.md` — installed / deferred / rejected, and why.
- `2026-07-02- Daily Brief - Architecture Spec v5.md` — current architecture spec.
- `RUNBOOK.md`, `TEST-PLAN.md`, `MAINTENANCE-CALENDAR.md`, `INSTALL-GUIDE.md`,
  `How the System Works.md` — operations and build.
- `SOUL.md` — Hermes' identity file (the agent half).
- `COORDINATION-PROTOCOL.md` and `coordination/` — the canonical single-writer
  ledger and resource-lease protocol. Runtime state stays outside Git under
  `<nas-root>/run/`.

---

## Log what you learn

System-wide operational knowledge that is not in code or git history —
cross-repo coordination hazards, environment gotchas, token/CI limits — goes in
`OPERATIONS.md` here, one dated entry each, newest first. This is the canonical
home; a code repo's own `OPERATIONS.md` may duplicate the slice its agents need,
because a subagent inherits only its repo and cannot follow a cross-repo link.
Before finishing work that taught you something a future agent would otherwise
re-learn the hard way, add the entry.
