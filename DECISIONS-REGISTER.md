# DECISIONS-REGISTER.md — master decision record

> **Note on this public edition.** This is the working decision log of a system
> running in one real household. Three kinds of change were made for
> publication, and nothing else:
>
> 1. **Names replaced with roles** — "the operator" (the primary operator, who
>    authored and approved most of these rulings), "the second operator", "the
>    children".
> 2. **Home-network detail removed** — internal addresses, subnet layout, VPN
>    device names, proxy host ids, the location of disablement secrets, and the
>    household's own domain. Where a row's *argument* depended on that detail,
>    the argument is preserved and the specifics are described generically. One
>    row (a second LAN origin for the internal web surface) was cut to a summary
>    for this reason.
> 3. **A physical-absence window removed** — one row originally recorded the
>    dates the house would be empty.
> 4. **School-specific vendors described by role, not name** — "the gradebook
>    portal", "the LMS", "the school-comms portal", "the cafeteria portal". The
>    combination would narrow the district; the individual names carry no
>    argument. Identifiers derived from them were genericized to match.
> 5. **Exploitation detail removed from live-gap rows** — this register records
>    accepted security trade-offs as decisions, which is the point of keeping it.
>    Where a row described a *currently open* weakness at a level that would make
>    it actionable — module chains, activating conditions, what a given surface
>    does and does not authenticate — the decision, the reasoning and the lesson
>    are kept and the operative specifics are omitted. Every such omission is
>    marked inline. Closed or avoided vulnerabilities are described in full;
>    there is nothing to protect there, and the reasoning is the valuable part.
>
> Reasoning, dates, dollar figures, rejected options and recorded mistakes are
> unchanged. Several rows reference internal spec documents that are not part of
> this publication.

## Coordination control plane (2026-08-19)

| ID | Decision | Status | Rationale |
|----|----------|--------|-----------|
| COORD-1 | Sessions use isolated worktrees; no shared-checkout stash/rebase | Approved; candidate pending audit | Shared mutable checkout caused cross-session loss/races |
| COORD-2 | SQLite atomic task/resource leases with `WAITING` contention state | Approved; candidate pending audit | Prevent overlapping repository/NAS mutation without treating normal contention as failure |
| COORD-3 | Immutable event inbox + singleton ledger publisher | Approved; candidate pending audit | Preserve simultaneous findings exactly once; agents never commit the ledger directly |
| COORD-4 | One leased `MERGER-EXECUTOR` performs all GitHub/NAS mutations | Approved; candidate pending audit | Centralizes merge permission and serializes the low-volume mutation lane |
| COORD-5 | Separate coordination repository + rendered Markdown ledger | Deferred until COORD-1–4 prove stable | Removes coordination-only movement from HOMER main, but is a larger migration than the immediate collision fix |
| COORD-6 | Database-backed compact hot context; Markdown ledger becomes cold audit archive | Approved; implementation candidate pending audit | Routine startup/polling uses bounded `overview`, `task-context`, and `event-show` reads so agents do not repeatedly load the multi-megabyte historical ledger; history and the singleton publisher remain unchanged |
| COORD-7 | Lease resources use immutable repository IDs and stable component roles; current and legacy names normalize once at ingress | Approved by the operator; implementation candidate pending audit | Repository or checkout renames cannot create a second lock domain, and `repo:homer:main` remains permanently governance-only |

## Hardening (Phase 1, all INSTALLED via expanded-features)
| ID | Decision | Status | Rationale |
|----|----------|--------|-----------|
| C1 | LMS pagination (follow rel=next) | Installed | Don't drop paged data |
| C2 | Nested gradebook-portal error shapes | Installed | Treat as fetch-fail, not data |
| C3 | Per-source state carry-forward + flag | Installed | Failed fetch must not poison baseline |
| C4/C8 | CalDAV expansion flag + timeout | Installed | Bounded, safe fetch |
| C5 | Email truncation info-flag | Installed | Signal when body cut |
| C9 | Import-time crash shim (raw Telegram via os.environ) | Installed | Startup failures still alert |
| C10 | Kid-attribution collision guard | Installed | Flag, don't guess who |
| C11 | Grade keys include period | Installed | No cross-period collisions |
| C12 | Per-source try/except in normalize | Installed | One bad source ≠ dead brief |
| PPV | Quarantine every blind-built parser 2 wks; <50% → log-only | Installed | Silence is earned |
| #25 | Metrics foundation (private, never a score) | Installed | Enables all reviews |

## 25-rec walkthrough outcomes
| ID | Item | Status | Revisit |
|----|------|--------|---------|
| #25 | Outcome metrics + quarterly guided review | Installed | — |
| #12 | /sick | Installed | — |
| #11 | /med | Installed | — |
| #24 | Guided Sunday review | Installed | — |
| #15 | One-on-one nudge | Deferred | 2026-09-13 |
| #16 | Process-praise cards | Deferred | 2026-09-13 |
| #17 | Transition warnings | Deferred | 2026-09-13 |
| #18 | Worry parking | Deferred | 2026-09-13 |
| #19 | Repair nudge (Playbook covers now) | Deferred | 2026-09-13 |
| #23 | Errand batching (needs Todoist) | Deferred | 2026-09-13 |
| #2 | Training-block proposer | Deferred | 2026-09-13 |
| #20 | Family-council agenda | Deferred | 2026-09-13 |
| #1 | Movement ledger | Rejected | surveillance-y |
| #3 | Conditioning ramp | Rejected | out of scope |
| #4 | Monthly challenge | Rejected | gamified |
| #5 | Dinner-variety scorecard | Rejected | counts a win |
| #6 | Pack-lunch rotation | Rejected | low value |
| #8 | Grocery gap detector | Rejected | AnyList's job |
| #10 | Sleep-compression detector | Rejected | surveillance-y |
| #13 | Heat/AQI alerts | Rejected | noise |
| #14 | Checkup prep journal | Rejected | low value |
| #21 | Village ledger | Rejected | low value |
| #22 | Equity snapshot | Rejected | replaced by quarterly reflection prompt |
| #7 | Sports-day fuel | Reshaped | → GEAR_RULES edit |
| #9 | New-food Friday | Reshaped | → Hermes Playbook monthly pattern |

## Rec #25 sub-decisions (2026-07-15, at build time)
| Item | Decision | Why |
|------|----------|-----|
| Prompt caching on the pipeline call sites (6i(2)) | **Rejected** | Only a *prefix* caches. The longest static template (`prompts/morning.txt`) is ~1,630 tokens; the minimum cacheable prefix is 2,048 on Sonnet and 4,096 on Haiku. Only the static template is eligible — the per-run payload sits after it and changes every run — so request size is irrelevant: the measured morning request is 8,553 input tokens and still cannot cache, because the *prefix* is 1,630. A `cache_control` breakpoint would therefore be a **silent no-op** — no error, `cache_creation_input_tokens: 0` forever — and the blocking test (`cache_read_input_tokens > 0` on a second consecutive call) could never pass, on either tier. Prize was ~$0.08/mo. Revisit only if the prompts grow past the minimum; the breakpoint then goes on the STATIC block, and "caching is enabled" is not the criterion — cache HITS are. |
| Model tier for pipeline calls (6i(2)) | **Sonnet, not Haiku** | The spec's safety argument ("the validation layer already covers the extractors") is a **precision** argument: `validate_actions`/`validate_notes` are add-only and profile-checked, so they reject a *wrong* item. They cannot detect a *missed* one — recall is unprotected. A school note Haiku fails to extract validates cleanly and is indistinguishable from a quiet inbox, which is the prime-directive failure. Cost, from the live token record rather than the pre-build estimate: ~$3-4/mo on Sonnet, and Haiku's rates are exactly 1/3 of Sonnet's ($1/$5 vs $3/$15), so ~$1-1.3/mo — call it **~$2-2.7/mo to remove a recall risk on the kids' deadlines**. (The decision was originally taken on an estimate of $0.64 vs $1.91; the ratio held, so the call stands, but the absolute numbers were 3.5x low — see the COST_BUDGET_MONTHLY row.) `MODEL_NARRATION` keeps the seam — flip one env var and the 6i(3) per-call-site token records price the alternative exactly. |
| COST_BUDGET_MONTHLY | **$10, not $50** | At $50 the 80% trigger is $40 — a margin the guard would never reach, i.e. an alarm wired never to ring. Set from the first live token record (2026-07-15): one morning narration is 8,553 in / 1,316 out = **$0.045**, so the five call sites run ~$3-4/mo once school mail resumes. $10 puts the trigger at $8, ~2-2.5x that. NB the pre-build estimate said $1.91/mo and was **3.5x low on input tokens** — it measured a payload with `prev == m`, so the delta was empty and the extractors were off. $5 was briefly chosen on that bad number and would have risked firing on a normal September. Tune from token records, never from an estimate. |

## Roadmap phases
| Phase | Decision | Status |
|-------|----------|--------|
| 2 | Small wins (/todo, /sick, /med, GEAR_RULES, 21:00 stop) | Build post-cruise |
| 3 | 20:30 close-of-day (subsumes 20:00 + 20:45 standup) | Build |
| 4 | Shared-context bridge | Build |
| 5 | Telegram inline buttons | Build |
| 6 | Draft handoff (+ draft_email allowlist only) | Build |
| 7 | Todoist projection (API v1) + guided Sunday review | Build; trial gate 2–3 wks after school |
| 8 | Review layer (Saturday research, quarterly review, liveness) | Build |

## Phases 8–14 — interactive suggestions, flag-watch, mutual awareness (2026-07-19)

**Design stance: deterministic floor + model breadth.** Code gates (senders,
keywords, forwards, flags) are the guaranteed, auditable recall FLOOR; the LLM
sweep adds breadth on top and is never a replacement for a code gate. A model
miss is invisible; a code-gate miss is diagnosable. **Never remove a code gate
because "the sweep covers it."** Every new detector serves the 14-day log-only
PPV quarantine; every new source/actuator gets an env kill switch; every new
state key carries the poison-update-loop guardrails (caps, TTL, sticky-dismiss,
adversarial tests). No new LLM call sites — 8c rides the ONE action-mail
extractor call.

| ID | Decision | Rationale |
|----|----------|-----------|
| 8a | Operator-forward gate (`OPERATOR_EMAILS`) makes a co-operator's forward a candidate | A forward from either operator is a delegation; it hit no gate before (the 2026-07-19 camp-email miss). Unset env → inert, zero change. |
| 8b | Extractor runs on candidates OR gearless OR sweep, not only candidates | The Phase-6 gear pass rode the same call and could never fire on a zero-candidate day; `gear_rules_learned` stayed empty in prod. |
| 8c | All-mail `needs_action` sweep — pure-code candidate selection, evaluate-once (`action_swept` cap 200 FIFO), sweep pile cap 12, `MAX_CANDIDATES`=20 (gates first) | Model breadth on ordinary inbox mail the gates miss; the deterministic floor is never crowded out. `needs_action` is a new detector → 14-day log-only cage (`QUARANTINE_NEEDS_ACTION`). |
| 8c-cost | **Sweep cost line:** ~35 emails/day post-filter, sweep-once semantics → steady state is only NEW mail each day through the same Sonnet call; order-of-$5/mo. Tier stays Sonnet (recall argument, register 6i(2)). | Do not "optimize" to a cheaper tier from an estimate — the recall of the sweep is the product. |
| 9 | Planning-gap detector (`PLANNING_GAPS`) — deterministic TBD/`??` match on operator-created calendar text; one offer per `uid` ever | A precise match on the operator's own text (PPV=1), so unquarantined — like the awaiting core. Excludes `homer_generated` + school-feed (no uid). |
| 10 | Pending-suggestions queue — two-tier delivery, **replace-don't-add** | Attention is budgeted. An item dated within the next-brief horizon (today/tomorrow) stays INLINE exactly as today (.ics, legs, full line); everything else rides the queue behind ONE footer line that STANDS IN FOR the inline lines those items would have been. The footer replaces, it does not add a slot. Overflow drops oldest and is COUNTED (ledger line); TTL 7d expires silently; `suggestions_dismissed` is sticky. `needs_action` tasks surface via the queue (their batched channel) and are excluded from the inline nag — they are replies/decisions, not time-critical .ics logistics. |
| 11 | Flag-watch (`FLAG_WATCH`): a starred (`$flagged`) email → `flag_followup` task; self-clears when unstarred or gone from the window | The star means "needs action"; human-initiated, PPV=1, unquarantined. The star itself is the user's — TTL/window clears the TASK, never the STAR. |
| 11b | **Flag-clear tiers** (actuator `fetch.clear_flag`, `FLAG_CLEAR` default OFF): (1) EXPLICIT — /done on a flag task (typed/tapped/Todoist/Hermes `flag_cleared_ok`) clears immediately, "(star cleared)"; (2) INFERRED — a detected reply surfaces a one-tap "clear the star?" button, never auto-clears; (3) TTL/expiry NEVER clears. Scope: removes ONLY `$flagged` from a single TRACKED id, never `$seen`, never another message. 14-day probation = `FLAG_CLEAR` off → logs "would clear" only. | The pipeline's first mailbox write; scoped as hard as JMAP allows and probation-gated. Every mailbox write is visible. |
| 12 | Commands cadence 15→5 min (cron id 13 `2-59/5`, middleware not git); free-text redirect (canned, no LLM, ≤1/chat/hour) | Detection frequent, attention batched — polling is not pinging. Non-command text died silently before; a co-operator gets one pointer to Hermes. Non-allowlisted traffic stays silent (never confirm the bot). |
| 13 | Reverse handoff (`REVERSE_HANDOFF`): Hermes drops enumerated-verb outcome files in `briefs/handoff/inbox/`; pipeline applies + moves to `done/` | Only the pipeline writes state (rule 7). Hermes-written files are UNTRUSTED input (wave-3 class): closed verb set, `id` must reference an existing entry, `note` scrubbed and NEVER reaches a prompt, ≤20/tick, malformed→archived+counted, never a retry-loop. |
| 14 | Mutual activity awareness: pipeline→Hermes `activity` ledger in the snapshot (48h, cap 30); Hermes→pipeline `hermes-journal.jsonl` (bounded 64KB/50-entry/48h read, scrubbed, rotated to 7d) | Each half knows what the other did. Journal text is untrusted (same scrub as snapshot fields); it feeds extractor dedup context + a `hermes_activity` narration line. |


## Phase 15 — narration clusters: fold duplicates in presentation (2026-07-29, APPROVED — build gated)

**Design stance: presentation power, never omission power.** The items-mode
narrator (11c177a) may additionally propose that same-thing items fold into one
line. Code verifies every member, stamps every fold visibly ("(also: …)"),
renders everything else, and can render every member individually at any time.
The model gains no power to drop, gate, reorder, re-section, or choose
quiet/deliver — folding is phrasing. Goal in operator terms: duplicate-heavy
days read shorter with nothing lost, at zero added operator work. Spec:
"2026-07-29- Narration Clusters Spec.md". Amends invariant #1 wording in three
doc sites (spec §9; dailybrief/CLAUDE.md copy rides the clusters PR).

| ID | Decision | Rationale |
|----|----------|-----------|
| 15a | Reply gains optional `clusters: [{ids, line}]`; every id still answered in `lines` (overlay, never replacement) | One model contract across shadow/armed; the items fidelity log keeps meaning |
| 15b | Validation is code-owned: offered ids only, allowlist kinds {cal, note, mail, prop}, 2–6 members, ≤8 clusters, no overlap (first-wins), absolute-date agreement, scrubbed line; any failure renders members individually | Every foldable kind is action-free and safety-free; flags/tasks/bills/decisions can never fold; safe failure is duplication, never loss |
| 15c | Render = replace at earliest member's slot + code-stamped provenance; emptied sections lose their header; conservation identity checked at runtime — any failure discards ALL clusters for the run | The fold is visible to the family by construction; degrade, never trust |
| 15d | `NARRATION_CLUSTERS` ∈ {0 (default), shadow, 1}; receipts to briefs/cluster-receipts.jsonl (30d) + counts in log.jsonl | Kill switch + reviewable evidence; no new notification slot, no new operator chore |
| 15e | Gates: build after clean items-mode fidelity week; shadow may start early (free QA) but arm evidence counts only from real school traffic (≥14d AND ≥10 proposals); arm = the operator's explicit flip at zero false merges | The maturity question is encoded as gates, not argued away |
| 15f | **Build-gate deviation, 2026-07-30 (operator, recorded):** built on 3 clean fidelity days (48/48, 40/40, 39/39), not 7. Accepted because the code ships behind `NARRATION_CLUSTERS=0` and arming still requires the human flip at zero false merges under real school traffic — the gate's purpose (never arm on shaky fidelity) is untouched; only the authoring moved earlier, to spend available capacity while it existed. The fidelity week is still required before `shadow` runs against school traffic. | A gate protects an OUTCOME, not a calendar; deviations get recorded, not rationalized away |

## Phases 16-18 + research addendum (2026-07-29, APPROVED — sequencing in "2026-07-29- Master Execution Plan.md")

| Phase | Decision | Status |
|-------|----------|--------|
| 16 | Connective tissue: proposal conflict-check, calendar-delta re-checks, brief archive, co-operator onboarding (1e) | Build |
| 17 | Importance overlay (rank within code pins; spec ~Sep 1) | Draft Sep |
| 18 | Assistant surface: snapshot v2 + Hermes judgment-on-demand | Build after 15/16 |
| R | 14 research recommendations ("2026-07-29- Assistant Landscape Research.md"): capture, delta stamps, symmetry extensions, freshness, /fewer, evening-load, mailbox parity, PPV ids, strict-mode, rollover verbs, kill switches, PII filter, drift check, disclosure | Build per Master Execution Plan; #2 gated post-cluster-arm |
| S | HOMER surface: content lane = NAS static PWA over a mesh VPN (ACL-scoped, with tailnet-lock device signing; zero public reachability — the WAN-exposure rule's spirit holds, third-party control plane accepted as a documented trust dependency); alert lane = HA companion push trial, Telegram keeps heartbeat + fallback until earned. **Device signing ENABLED 2026-07-31** — a small set of trusted signers, with disablement secrets held offline; every new device (the second operator's, the children's) must be signed from the NAS before it can connect. 2026-07-29 PM (operator): VPN scope widened from HOMER-surface-only to tiered lab access — the NAS advertises the household's general-purpose subnets; ordinary members reach the brief/HA/proxy-fronted services only, an admin group reaches the subnets; the **camera VLAN stays OFF the VPN permanently** — DECIDED 2026-07-31: cameras flow through their NVR as the gateway; the VPN never learns they exist | Build; bell-swap decision Oct |
| S1 | **Surface freshness — a silent hourly lane beside the loud starred one** (operator, 2026-08-01; full spec: "2026-08-01- Surface Freshness S1 Spec.md"). Governing rule, the operator's words: *"only starred emails get action, the rest are silently updated."* **That is the SHIPPED pattern, not a new one** — `run_commands` (cron 13) already fetches `fetch_flagged_inbox` (`brief.py:3658`), already spends an LLM call on a genuinely-new star, and already sends (`brief.py:4320`). Its docstring claiming "No source fetches, no LLM, no independent notification slot" is **stale on all three clauses** and has misled two design passes; fixing it is a work item. So S1 extends a lane rather than inventing one: email + calendar hourly, 05:30–21:00 (the 21:00 bound is principle 8), running the EXISTING `extractor_actionmail` — no fifth call site — writing state and re-rendering the page, and **sending nothing**. **The LMS and the gradebook portal were specced for an hourly tier and the operator REMOVED them** — scheduled runs only; do not re-add (the LMS paginates at 30s/request and is the one source that can push a tick past a minute; grades are not hour-actionable). schoolcomms stays out permanently on account risk — full parent-account auth POST every run, no cached bearer. Header gains per-source "as of" stamps; uniform-looking freshness over staggered data is the staleness-is-invisible failure the no-service-worker rule forbids. **Two hazards, both enforced in code:** (1) the silent lane touches no send path — the page is pull, Telegram keeps its four scheduled sends plus starred; a fresher page must never mean a busier phone (rule 4). Load-bearing test: a tick that fetches new mail and re-renders sends ZERO messages, while a new star still speaks. (2) Lock contention — cron 13 is `flock -n … timeout 180`, scheduled runs `flock -w 60`, so a long tick silently kills the morning brief (the 2026-07-15 failure, ~38% of briefs lost). Raise scheduled runs to `-w 300` AND skip the hourly lane adjacent to a run boundary; this lives in NAS middleware, so CI cannot verify it. **Cost, measured not estimated** (`briefs/log.jsonl`, 18d): `extractor_actionmail` floor ~3,220 tok, sweep-once means more calls repay only the floor; at live `gpt-5-mini` rates this adds **~$0.14–0.36/mo**. | Build; approved 2026-08-01 |
| S1-findings | **Two out-of-scope facts S1's costing surfaced.** (1) **Model tier — RESOLVED 2026-08-01 (operator, asked directly): "we are not using Sonnet."** `MODEL_NARRATION=gpt-5-mini` is the deliberate live choice; Rec #25's "Sonnet, not Haiku" row is the STALE half and its dollar figures ($3-4/mo, the COST_BUDGET_MONTHLY sizing, the 8c-cost ~$5/mo sweep line) are all argued in Sonnet dollars that no longer apply — read them as history, never as current cost. Price from `briefs/log.jsonl` token records instead. Rec #25's recall ARGUMENT is untouched by this and still stands as the thing to watch: a smaller model's miss is invisible and reads as a quiet inbox, so extractor recall is the metric to keep an eye on, not spend. (2) **The cost alarm cannot ring.** Measured total spend is ~$0.16/18d ≈ **$0.27/mo** against `COST_BUDGET_MONTHLY=10` (trigger $8) — "an alarm wired never to ring", the exact failure this register cited when rejecting a $50 budget. It was sized for Sonnet and never re-sized after the model changed. | Open — decide separately |
| S2 | **Surface free-text box, one per action item, revealed on demand** (operator, 2026-08-01). Not always visible — a card shows its normal chips until a disclosure control is tapped, then one box for "actually, do it this way instead". Submitted text is a message to Hermes about that item, never a direct state write: the receiver enqueues, the 5-minute tick applies, hard rule 7 (single state writer) unbroken. Carries the first inbound write path on the surface, so it lands with the CSP `connect-src`/allowlist story and the same prompt-door sanitization obligation the delivery mirror carries. | Build next |
| S3 | **Surface becomes the single interaction place; Telegram retires to alert-lane only** (operator, 2026-08-01). End state: every verb the family uses — structured taps (Done/Snooze/Not this), free text per item, and conversation with Hermes — happens on the internal page; Telegram keeps only the dead-man's-switch heartbeat and the fallback when the page is unreachable. Prerequisites, in order: (a) S2 free-text box shipped and used; (b) an inbound write path with a real auth story beyond network reachability (a shared page is a shared identity — the second operator, and later the children, need distinguishable actors for the two-operator principle #7); (c) the page becomes a full prompt door, inheriting the `deliver._spool_mirror` sanitization obligation for *conversation*, not just item text; (d) the page must never become a notification slot — retiring Telegram removes push, so the replacement push lane (HA companion, row S alert lane) must be earned FIRST or the family simply stops being told anything. Do not start before S2 has run long enough to show the free-text box is actually used. | Future — not scheduled |

| S4 | **Second origin: the surface is now served on the home LAN as well as the VPN** (operator, 2026-08-02). *(Configuration specifics — hostname, proxy host id, container address, monitor id — removed for publication.)* A static read-only mirror of the same generated site was fronted by the household reverse proxy on the LAN, because a home desktop had no VPN client. **The operator was told what the page exposes and what it lacks by way of access control of its own, and accepted the widened reach explicitly** — the point of the row is that the consequence was named at decision time rather than discovered afterwards. The document-email `View:` links deliberately continued to name the VPN host. | This narrows row S's "zero public reachability" to *public* only: reachability is now anything on the home network, decided by firewall rules rather than by VPN membership + device signing. It is **not** a WAN exposure and does not touch the standing no-port-forward rule (below) — the proxy is LAN-bound and the household's names resolve LAN-side only. The real cost is booked against **S3(b)**: that row's prerequisite already said "a shared page is a shared identity"; with a LAN origin the page is not even VPN-gated, so the auth story is now owed *before* any inbound write path, not merely before Telegram retires. |

| Feature | Kill criterion |
|---------|----------------|
| Narration clusters | 1 confirmed false merge post-arm → shadow + register entry; median <2 folded lines/wk by 2026-10-15 → strip |
| Calendar conflicts | >2 flags/wk & <half acted → TRANSITION_MINUTES=15 or gap warnings off |
| School notes | <~50% relevance over 2 wks → SCHOOL_NOTES=0 while tuning |
| Social ledger | unfed by January → strip |
| Standup | consolidation is the fix (done in Phase 3) |
| Hermes needs-a-reply job | → on-demand/weekly (done) |

## Open-source release — doctrine first, code maybe (2026-07-30, APPROVED direction, gated)

The operator's intent: HOMER's thinking should reach the OSS community; friends'
interest validates demand but non-technical users are NOT the audience (they
need hosted SaaS, which the privacy model — family data never leaves the
house — rules out; running a service for others is a principle-9 violation).

| Item | Decision | Gate |
|------|----------|------|
| OS-1 | **Publish the doctrine**: invariants, SOURCE CONTRACT, architecture spec, audit methodology (six-lens + adversarial verify + dispositions-ledger protocol), lessons learned — as a public docs repo / essay series. Candidate flagship piece: the 2026-07-30 three-AI audit-and-fix loop (one model authors, a second reviews, a third merges/deploys, git ledger as coordination substrate) as a case study. Reference posture: "how we built ours," no support promise; kill criteria apply to the OSS project itself. | ~~Fall 2026, AFTER the C1 trust boundary closes (brokers + terminal/file strip + /yolo + egress enforcement). Never publish the blueprint mid-hardening.~~ **Gate RESOLVED 2026-09-23 by C1-CLOSE:** C1 closed by acceptance, so there is no mid-hardening state left to wait out and the TIMING gate is discharged. What remains is a CONTENT judgement, which the original gate did not distinguish — that judgement is applied per-document in this publication rather than as a blanket hold. |
| OS-2 | **Code release is a separate, later decision** — only viable after the A1 SourceRun refactor makes sources pluggable (today's integration lattice is bespoke to one district/stack). Revisit ~winter, informed by whether OS-1 draws real builders. | Post-A1 + OS-1 signal |
| OS-3 | **The live repos are NEVER flipped public.** Git histories, commit messages, PR bodies, ledger and docs carry the children's names, the district, schedules — correct for private repos, disqualifying forever. Any release is a clean-room export: fresh history, invented example family, scrubbed docs. From 2026-07-30 forward, write new code and docs generic-by-default to keep that export cheap. | Standing rule, effective now |
| OS-4 | Hosted service for non-technical users | REJECTED — belongs to whoever builds on the OSS release, not to this household |

## F2 document email — the operator email list is a delivery channel (2026-07-31, APPROVED and LIVE)

Ratified by the operator 2026-07-31 at the `DOC_EMAIL` flip, as the F2
disposition required. The wording is the decision:

> The config-pinned operator email list is an in-household delivery channel,
> not a third party: a document email to an address on that list is delivery to
> an operator, not disclosure outside the household. Any recipient beyond that
> list still requires human approval per invariant 3.

What that ratifies concretely: the pinned list is the two operators' addresses
from `OPERATOR_EMAILS`, and the second operator's is hosted at a major webmail
provider — so school PDFs (report cards, placement letters, testing reports)
egress to that provider. Named at approval time, not discovered later. This is
the second operator's interim channel until Telegram onboarding and a keeper
after; it is an instance of the mutual-awareness per-operator `channels` model,
not a special case.

The list is config-pinned for a reason: the recipient set is code, never model
output, and F2 re-pins an unsent intent against the registry before egress, so
an address removed from `OPERATOR_EMAILS` can never receive a later send.
Adding an address is a config change and therefore a human decision (invariant
6). Sending anywhere else is not a config change — it is a new decision.

LIVE as of 2026-07-31: `DOC_EMAIL=1`, `SURFACE_URL` set to the internal surface,
`FASTMAIL_SUBMIT_TOKEN` minted with submission scope and revocable
independently of the read path. Kinds are `report_card,placement,testing`.

## F4 — document capture as a first-class gate (2026-08-01, DEFERRED — scoped, not scheduled)

Today document capture is a **passenger** on fetches that exist for something
else. The link follower runs to feed dates to the school-notes extractor; the
attachment fetch runs to feed the action-mail extractor. Both keep a copy only
incidentally, and both inherit a selector tuned for the extractor's appetite
rather than for "is this a document worth keeping". The consequence is that the
store's coverage is an accident of two unrelated gates.

Two concrete gaps, same root cause:

- **Links that are not `.pdf`.** `_PDF_LINK_RE` requires a literal `.pdf`
  suffix, so tokenized and handler-style download URLs (the school-comms portal
  attachments, `?attachment=`, `/download/`) are invisible. Compounding it, the
  host allowlist is the school senders plus the news host and its registrable
  parent — school newsletters routinely go out via CDNs that are not on it.
  **The host allowlist misses more real documents than the suffix rule does.**
- **Attachments the extractor does not want.** `actionmail.wants_pdf` fetches
  only on form/invite/registration mail. A "report card attached" email is
  never downloaded at all, so no amount of classification helps.

| ID | Decision | Rationale |
|----|----------|-----------|
| F4a | **Keep the linked PDFs already downloaded** — delete the `schoolcomms:`-only keeper gate; label by id prefix (`email`, `schoolnews`, `schoolannounce`). BUILT 2026-08-01. Spec: "2026-08-01- Spec Draft - F4a document capture (link keepers).md" | No new fetch, host, URL shape, prompt input or cost. The bytes are already past the allowlist, the SSRF pin and `Store.put`'s `%PDF-` check — the only change is whether they are written down. |
| F4a-scope | **Classifying mail attachments was scoped, then WITHDRAWN before build.** It would have reverted `ae1f4b7` ("audit: GB-1 harden mail PDF provenance"), which pinned `_keep_mail_pdf` to `kind="other"` that same morning to close Gate-B **GB-3**. `kind="other"` stays. | GB-3's argument is **provenance, not classification**: `classify_kind` reads the title, so the lane is safe only when the title's author is trusted. `actionmail.classify` admits form/invite/registration keyword hits **from any sender**, so a stranger's subject line can promote their attachment into `DOC_EMAIL_KINDS` and get it re-sent from the family's own identity. The link lanes F4a widens are all gated upstream — `school_mass` is domain-anchored to `Config.school_senders` via `parse.is_school_sender`, and the three portal feeds are the school's own — **so widening them is not the same act as widening the attachment lane.** Recorded because "make the two keepers consistent" is the obvious wrong next move. |
| F4b | **A capture gate independent of the extractors.** Per-host URL *shape* allowlist (enumerated patterns added only after being seen in a real message, never speculatively), plus a sender/filename gate for mail attachments that does not ride `wants_pdf`. New fetch path → own env kill switch, own cap, per-candidate budget so one link-heavy newsletter cannot eat the run. | The suffix rule is **not a security control** — the host allowlist, per-hop DNS resolution with IP pinning, manually re-validated redirects and the 5 MB stream cap are. Dropping the suffix costs budget dilution, store/surface pollution, and login-page fetches on session-gated URLs; it does not open SSRF. So the design question is *selection quality*, and "follow every link" answers it badly. |
| F4c | **Content sniffing is already handled — do not rebuild it.** `Store.put` rejects any body without `%PDF-` in the first 1 KB (`lib/documents.py:169-171`), and `lib/pdfparse` parses in a resource-bounded subprocess. `on_pdf` does fire before parsing on all three fetch paths, but the store is the choke point and it holds. | Recorded because it looks like a gap on inspection and was mis-called as one on 2026-08-01. A widened selector needs no new sniffing layer. |
| F4d | **Prerequisite, not optional: close the redaction-floor gap first.** The outbound kid-PII floor covers the messaging channel only; the document-email egress does not honor it. Widening document capture would push more report cards through the un-floored channel. *(Module chain omitted for publication.)* | The gap is inert only while the floor's denylist is unpopulated. Populating it would make one channel start redacting and the other silently not — the exact shape of failure invariant 2 exists to forbid, and a worked example of why a partial control is worse than a declared absence. *(The activating condition is omitted for publication.)* |

**Kill criterion:** if the Documents tab is not opened in a month, or half its
rows are things nobody wanted kept, the widened selector is noise with a storage
bill — revert to the `.pdf`-suffix rule and keep F4a only. Coverage is not the
metric; a shelf someone actually uses is.

**Gate:** not before F4a has run a full school month, so the selector is widened
against observed link shapes rather than imagined ones. Sequence after S2.

## S2 receiver — a new minimal container, built dark (2026-08-02, APPROVED)

**Decision (operator, 2026-08-02): Option A — the surface's inbound write path is
served by a NEW minimal container, and it stays DARK until the operator is back
from an extended absence.**

What was decided against, and why, so it is not re-opened:

- **Inside an existing container — rejected.** The only always-on process in the
  serving path is the VPN daemon, which runs the vendor's own image. Anything
  added there is wiped by an upgrade. Effectively disqualified rather than merely
  worse.
- **No listener at all — rejected.** It declines S2 and leaves S3 permanently
  blocked, since retiring Telegram to the alert lane requires the page to accept
  input.

**Mechanism.** The VPN's serve layer currently maps `/` to a static file target,
and that mount is READ-ONLY, so the serving side cannot write and a static
target cannot accept a POST. The unlock is a SECOND serve mapping — `/submit`
proxied to a localhost port — pointing at the new container. The serve layer
supports URL targets and path-scoped mappings. The page's CSP already permits
it: `default-src 'none'` plus `connect-src 'self'` (added by F4) covers a
same-origin `/submit`, so no CSP change is needed.

**Why DARK, and this is the load-bearing half of the decision.** S2 is the first
inbound write path this system has ever had, and its only credential is "you can
reach the private network". The operator was about to be away for ten unattended
days, with a change freeze immediately before. Standing up a new 24/7 listener
into that window trades a real new attack surface for a feature nobody is present
to watch. Built-dark costs nothing: code lands and is tested, the second serve
mapping is simply not created and the container is not started, exactly as
`DOC_EMAIL` and `INTRADAY_FETCH` sit merged and inert today.

**Accepted cost of Option A**, recorded rather than discovered later: a new
always-on service is one more thing that can die silently, which is this
system's signature failure mode. It needs its own health check before it is
armed — an unmonitored listener is worse than no listener.

**Ownership split**, mirroring S1: the container, the serve mapping and the
health check belong to the infrastructure agent; the page control and the
tick-side apply belong to the pipeline agent. The receiver ENQUEUES only — the
5-minute tick applies, so hard rule 7 (single state writer) is unbroken.

**Auth, stated plainly:** the operator confirmed the second operator's input
need not be distinguishable from theirs, which removes S2's hard problem. It
does NOT satisfy S3's prerequisite (b), which requires distinguishable actors —
so S3 stays blocked on exactly that, and this decision does not advance it.

## the cafeteria portal cafeteria source — isolated browser, no payment authority (2026-08-18, APPROVED)

The operator approved a 16:00 alert when either child buys more than one lunch
entree or buys water or milk. The source uses a dedicated the cafeteria portal account
created with no payment method on file.

| Item | Decision | Rationale |
|------|----------|-----------|
| MSB-1 | Keep the family-facing Hermes `browser` toolset disabled. A pinned, deterministic Playwright sidecar may navigate only login/verification and Cafeteria Purchases, then atomically hand DailyBrief a bounded raw table snapshot. | A general browser materially expands what a prompt can do. The policy needs one page read, not general browsing authority. |
| MSB-2 | The account must remain free of card, bank, autopay, stored-funds, cart, checkout, deposit, and payment authority. The helper contains no payment locators/actions. | Read access is sufficient. Removing payment capability bounds the damage of a credential or selector failure. |
| MSB-3 | Email 2FA may reuse the existing read-only Fastmail JMAP token, but only for messages received after login began with one exact sender, one exact subject, and one unambiguous six-digit code. The JMAP query is narrowed by sender and subject before retrieval, then exact equality is rechecked client-side. The code and email contents are never logged or persisted. CAPTCHA is human-only. | This automates the routine factor without turning the mailbox into an open-ended model tool or creating a CAPTCHA-bypass path. |
| MSB-4 | `lunch_spend` is deterministic and rides the existing 16:00 slot. `CAFETERIA_ACTIVITY` ships off. The detector ships and code-defaults caged; only an explicit `QUARANTINE_LUNCH_SPEND=0` releases it after 14 days of real, manually checked traffic. | No new notification slot; no model judgment; no historical alert flood even if the quarantine line is accidentally omitted. |
| MSB-5 | The raw snapshot is overwritten, not archived, inside a dedicated capture subdirectory of the existing DailyBrief backup boundary. The browser profile and five-key capture env live in a dedicated secrets directory outside the assistant container's broad host mount; their backup posture is separately **UNVERIFIED** and the profile is regenerable. | A root-running, prompt-reachable assistant container must not be able to read a new account password or remembered-device credential. Recovery convenience does not outrank the isolation boundary. |
| MSB-6 | The sidecar receives only its five-key env file, external profile mount, and dedicated output subdirectory. It never receives `dailybrief.env` or the full `briefs/` state directory, and its output path is explicit plus fixed-safe by default. | A third-party browser renderer needs the smallest possible secret and filesystem authority; a successful write into a disposable container layer must be impossible. |

## Claim temporality — `event` | `state` (2026-08-28, APPROVED WITH AMENDMENTS by PM; BUILT, NOT APPLIED)

| ID | Decision | Status | Rationale |
|----|----------|--------|-----------|
| TMP-1 | The accepted term is **`temporality`**, with exactly two values: **`event`** and **`state`**. Required on every claim in `homer-claim-v3`. | APPROVED 2026-08-28 | v2 had no way to distinguish a claim recording *something that happened* from one recording *something currently true*. |
| TMP-2 | `as_of` is redefined to mean **only** the UTC instant of the latest evidence observation or affirmative confirmation supporting that exact immutable version. Never occurrence, valid, ingestion or review time. | APPROVED 2026-08-28 | In v2 one field carried two meanings depending on the record, so every computation reading it — staleness, review, supersession — ran over a field whose meaning varied and was nowhere declared. |
| TMP-3 | Typed valid time: `event` requires `occurred_at`; `state` requires `valid_from` and may carry `valid_to`. An absent or null `valid_to` means an **open or unknown** endpoint, never "true forever". | APPROVED 2026-08-28 | Valid time and observation time are different questions and now have different fields. |
| TMP-4 | `review_after` is **state-only** and is a review **trigger only**. It never ends, invalidates or supersedes a claim. Events are excluded from overdue computation entirely. | APPROVED 2026-08-28 | An adjudicated historical fact has nothing to review; counting it as overdue is noise that dilutes a real signal. |
| TMP-5 | `supersedes` is **state→state only**; `corrects` is same-temporality only. Cross-temporality supersession or correction is prohibited. A factually wrong state is corrected; a genuinely changed state is superseded. | APPROVED 2026-08-28 | A worksheet does not "change into" a decision, and a past condition does not replace a present one. |
| TMP-6 | `temporality` is part of the identity, deduplication and contradiction key, so an event and a state about the same subject coexist without either silently replacing the other. | APPROVED 2026-08-28 | This is the case v2 could not represent at all. |
| TMP-7 | **No `immutable` volatility class.** `volatility` remains review-routing metadata only; it encodes no temporality and alters no truth. | REJECTED as substitute 2026-08-28 | It would have silenced the symptom while leaving the `as_of` ambiguity in place. |
| TMP-8 | All temporal fields use one canonical representation: offset-aware UTC at second resolution, `YYYY-MM-DDTHH:MM:SSZ`. | APPROVED 2026-08-28 | One representation means no comparison has to guess. |
| TMP-9 | **Built in the candidate user-model branch and NOT APPLIED.** v2 records are preserved byte-for-byte and load with `temporality: "unresolved"` in memory only — never defaulted, never persisted. | DECIDED 2026-08-28 (operator) | Reclassification is proposal-only. Of 219 records, 3 are batch-eligible and 160 need record-level review; applying them wholesale would bypass the human disposition the ruling requires. |
| TMP-10 | A migration creating v3 versions requires **separate authorisation** and must never rewrite v2 in place. | PENDING | `migration/temporality-proposal.json` holds the per-record proposal, rationale, evidence reference and before/after diff, awaiting disposition. |

**Why this exists, in one line:** two real misreadings occurred in a single session — a
point-in-time statement read as current status, and a mid-process worksheet read as a final
outcome. Both were `as_of` errors that the schema could not have caught. v3 makes them
structurally impossible to repeat silently.

**Known limit:** `as_of` cannot be migrated mechanically, because its v2 value cannot be known
to have meant observation time. A migration must obtain it per record. 102 of 219 records
additionally carry month-only precision that cannot become an instant without inventing a day.

## C1 trust boundary — broker NOT built, residual risk accepted (2026-09-23, DECIDED)

C1/B1 — the 2026-07-30 audit's largest structural finding — called for splitting
the family-facing agent from the admin agent, stripping terminal/code/browser/web
from the family profile, and adding network-level egress limits. Phase 0 shipped
2026-07-30. The remainder (the tool broker and its approval model, the
terminal/file strip that was explicitly "kept pending brokers", and egress
enforcement) did not ship, and per this ruling **will not**.

| ID | Decision | Status | Rationale |
|----|----------|--------|-----------|
| C1-CLOSE | The tool broker and approval mechanism will **not** be built. C1 closes by ACCEPTANCE, not by implementation. The OS-1 publication gate that named it is resolved. | DECIDED 2026-09-23 | The broker is a permanent new mechanism in the agent half — a mediation layer, an approval model, an acting-operator identity thread, and its own failure modes — carried forever against a low-likelihood risk. Principle 10 (when in doubt, do less) and the standing feature budget both say a mechanism that large must earn its place, and against a threat with no targeted adversary it does not. Complexity is itself a reliability risk in a system whose signature failure mode is silent breakage; adding an always-on mediation layer to prevent a rare event trades a certain maintenance burden for an uncertain one. |
| C1-KEEP | Phase 0 stands and is not revisited: eleven toolsets remain disabled. Manual tool approval, and denial of approvals to scheduled/cron-triggered runs, are hereby **load-bearing security controls**, not conveniences. | DECIDED 2026-09-23 | With the broker declined, these are the only controls standing between an injection and execution. Turning either off — or allowing a session-scoped/always approval to suppress the prompt — is a security decision, not a config tweak, and belongs in this register if it ever happens. |
| C1-ACCEPT | **Accepted:** the family-facing agent retains a broad local tool surface and unrestricted container egress. A successful prompt injection on an untrusted input path (mail from any sender, PDF attachments, portal content, or the delivery mirror) is therefore bounded only by the manual approval prompt. *(Configuration specifics — mount scope, credential locations and persistence mechanics — are deliberately omitted from this public edition; the decision is published, the exploitation path is not.)* | ACCEPTED 2026-09-23 | Recorded so the trade-off stays visible rather than being rediscovered, per this register's standing practice. The judgement is about LIKELIHOOD, not impact — impact is high and is not disputed. This is a private household system with no targeted adversary; the realistic vector is opportunistic rather than directed. |
| C1-CHEAP | **Identified, not decided.** Several mitigations exist that are not a broker and add no new mechanism — narrowing the credential and filesystem authority the agent is granted, and moving container egress to an allowlist. Each is configuration, not code. | OPEN | Listed because "no broker" is not the same decision as "no mitigation", and a future reader should not have to re-derive the cheap options from scratch. None is committed here. |

**Kill criterion for this acceptance:** it is reasoned from *low likelihood*, so
it should be re-opened if likelihood changes — the agent gains a new untrusted
input path with wider reach, the surface is exposed beyond the household network,
a third party gains agent access, or a real injection attempt is ever observed in
the wild. Re-open on changed exposure, not on general unease.

## Changeable knob defaults
OPERATOR_LIVENESS_DAYS=3 · PICKUP_WINDOW=14:30-16:00 · MED_LEAD_DAYS=21 · TASK_ESCALATE_DAYS=5 · TASK_REMIND_DAYS=5 · RECALL_LEAD_DAYS=21 · TRANSITION_MINUTES=30 · BILL_REMIND_DAYS=3. All are defaults, adjustable via the quarterly "adjust" decision.

## Accepted risks (decided, not oversights)
| Risk | Decision | Mitigation |
|------|----------|------------|
| Kid data transits Telegram (a third-party service) | ACCEPTED 2026-07-15 | First names only; grades summarized, never raw; no DOB, no medical detail. The rule lives in the narration prompts and SOUL.md — it is what the prompts are told to write, not a filter over what they wrote. Encoded so the trade-off is visible rather than rediscovered. |
| Document email links to the surface before the PDF is copied there (ORD-1) | ACCEPTED 2026-08-01 | `docmail.run` (brief.py) sends before `surface.write_site` copies the sha-named PDF into the served tree. Normally seconds apart in the same run; a crash between them, or `SURFACE=0`, leaves a delivered email whose `View:` link 404s until the next full render. **The PDF attachment rides the email regardless, so the failure mode is a dead link, never a lost document.** Reordering was rejected as the larger risk: it moves a send relative to a render inside a 5,600-line function. Revisit if a real document is ever reported unreachable. |
| Remote access to HOMER | Household VPN only; ZERO WAN port forwards to any HOMER service | Standing rule, verified at Step 1 baseline. A reverse proxy or tunnel is NOT an exception — it is the same exposure by another name. |

## Do not re-litigate without new evidence
The rejected list was decided deliberately (see DESIGN-RATIONALE). Re-open an item only with new evidence or a changed family situation. **Success metric: the system should be needed *less* over time** — declining context-switches and declining flags-raised, with kids trending toward self-monitoring.
