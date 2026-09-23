# Assistant Landscape Research — HOMER vs. the field

Date: 2026-07-29 · **Status refreshed 2026-07-30 — see the status block below before trusting any gap claim in this report.** · Method: 14-agent research workflow (7 web sweeps: big-tech, family products, OSS frameworks, OSS analogs, HCI/CSCW academia, LLM-agent academia, triage logic; ground-truth inventory from HOMER's own docs; adversarial register check; impact ranking). Full evidence: "2026-07-29- Assistant Landscape Research — Full Dossier.md". Web facts are as-of today; verify before acting on any single one.

## Where HOMER stands

Five dimensions lead the field outright. Conservation-checked narration (the model cannot drop, merge, reorder, or count) exists nowhere else; Apple's fabricated merged headlines and Huginn's silently dropped digest events are the field's proof of need. Code-enforced earned silence that can tell a quiet day from a dead pipeline (always-send 06:00 heartbeat, dead-man's switch, carry-forward baselines): no other surveyed system can. A numeric attention budget (≤4 context-switches/day, replace-don't-add, 21:00 stop) matches the Fitz 2019 batching RCT and is uncopyable by engagement-revenue vendors. The 14-day log-only PPV quarantine with sticky sub-50% auto-demotion has no analog anywhere. Parent-facing cross-portal school ingestion (the LMS, the gradebook portal, the school-comms portal, feeds) is unoccupied ground; COPPA/FERPA keeps platforms out. Also ahead: injection containment as architecture (effectively CaMeL with real Python as trusted planner), the strictest consent model surveyed, and human/code-owned single-writer state, at $7-11/mo, the bottom of the category band.

Behind the field (**as written 2026-07-29; six of these closed within 36 hours — see the status block**): no photo capture (2026 market baseline; paper never enters, the primary operator stays the scanner); no cross-source folding (the operator is the correlation engine); the co-operator's chat id unset, making deployed HOMER a one-operator relay; briefs unarchived; stale snapshots re-offered a closed camp three mornings; no importance layer; class-level tuning only via SSH env edits; PPV blind for id-less detectors; no core-fetch kill switches; parsers unverified until school resumes.

## Status as of 2026-07-30 (the 36-hour sprint)

Thirty-eight PRs merged, suite 1,545 green. Against this report's own ranked list:

| # | Recommendation | Status |
|---|----------------|--------|
| 1 | Paper-to-pipeline photo capture | **Built**, deployed, `PHOTO_CAPTURE=0` — pending Gate-C security review |
| 2 | NEW/CHANGED/CARRIED day-delta stamps | Not built — deliberately sequenced after clusters arm |
| 3 | Co-operator symmetry (Hermes access + her Drafts) | Not built — blocked on the co-operator's own setup steps |
| 4 | Snapshot freshness contract | **Live** (PR #100) |
| 5 | `/fewer` per-class volume verb | **Live** (PR #115) |
| 6 | Evening-load collision detector | **Built**, caged in its 14-day PPV quarantine (PR #113) |
| 7 | Co-operator mailbox as a source | Not built — needs her consent + credentials |
| 8 | Task-id instrumentation for PPV-blind detectors | **Live** (PR #111) |
| 9 | Strict-mode first-traffic window | **Built**, `STRICT_SHAPES=0` — flip ~2 weeks before school |
| 10 | Rollover [Today]/[Delegate] verbs | **Live** (PR #107). NB: the audit found [Delegate] never had ownership semantics even on the direct path — real delegation needs a task-owner model that does not exist |
| 11 | Whole-source env kill switches | **Live** (PR #102) |
| 12 | Deterministic outbound kid-PII filter | **Live** (PR #103), extended to every Telegram egress path incl. attachments and toasts (PR #132); inert until a denylist file exists |
| 13 | Standing middleware drift check | **Live and armed** (PRs #104/#136); cron id 28 at 05:40 |
| 14 | Register-change disclosure line | **Live** (PR #106) |

Also delivered but not on this list: the gradebook portal **Documents** source (the report never saw it — it was the sprint's highest-value find), narration clusters built inert, the static brief page over Tailscale, the brief archive, the dry-run verification harness, and the Gate-B audit that produced nine confirmed HIGH findings against code merged the same week.

## What building it taught us (added 2026-07-30)

The report framed HOMER's problem as **capability gaps**. Thirty-six hours of building says the dominant failure mode is **invisibility**, and that reframing belongs in any future assistant-quality thinking:

- **Three silent-failure classes surfaced in one day** — a page-render crash that logged nothing (cron discards stdout and no MTA delivers the mailed stderr), a placement letter deterministically dropped by a profile filter that could never match a next-year course, and a document source that would have permanently swallowed its own input on any extractor hiccup. None would have announced itself. All three are now structurally impossible rather than merely fixed.
- **A deterministic floor belongs under every model-dependent path.** Where the model's recall is unprotected by validation, code must guarantee the item surfaces *somehow* — even as its own bare existence. This saved the document source twice in one day.
- **"The class is closed" must be re-verified per new door.** Three hostile audits closed pipeline-text→prompt injection. A feature merged Wednesday re-opened it in a shape nobody thought to check (a delivery mirror replaying brief text into the agent's transcript). Closure is a property of doors, not of classes.
- **Review layers must apply to the author.** Twice in one day a fix agent found a defect in a fix written hours earlier by the orchestrator. The multi-layer review is structural, not ceremonial — and its value is highest on the most recently written code, which is the opposite of where attention naturally goes.
- **Gates protect outcomes, not calendars.** The clusters build gate was deliberately deviated from (built at 3 clean fidelity days, not 7) because the arming gate — the one that actually protects the family — was untouched. The deviation is recorded in the register rather than rationalized away.

## The landscape

Commercial. The family-logistics tier (Ohai, Skylight, Hearth, Maple; $7-30/mo) made photo/screenshot/forward capture the commodity baseline while ingesting no school portals: that asymmetry sets recommendation 1 and proves the niche empty. Maple's read-receipt shared inbox is the market's best co-operator story: visibility, never approval rights (hence 3 and 7). Alexa+ auto-adds silently, the anti-pattern HOMER's consent tiers refuse. Apple: Scheduled Summary and Focus are the deterministic interruption bar; Apple Intelligence's fabricated merged headlines (paused, returned with warnings, not fixes) are the proof against LLM merging (behind 2); Siri personal-context retrieval is the grounding bar (behind 4). Gmail Priority Inbox is the most instructive shipped system: explicit corrections above implicit signals plus a per-user volume threshold was its cheapest, highest-value component (error 45%→31%): recommendation 5. Shortwave bundles and Slack AI Recap prove deterministic grouping plus legibility stamps; Gemini Daily Brief's repetition proves LLM-ranked merging fails. Reclaim interrupts only when a defended commitment must move (behind 6). The 2024-26 shakeout (Milo, Dot, Yohana dead; Reclaim acquired; Motion to B2B) says the category dies commercially; self-hosting is the moat.

Open source. changedetection.io (32.5k stars) is the shipped convergence point (deterministic detection, LLM phrases the computed diff), validating the architecture and recommendation 2; its Ollama backend and OpenJarvis prove local narration viable (parked, not dead). Huginn's silent drops and Khoj's unconditional fires are the failures 9 defends against; OpenClaw's HEARTBEAT_OK is model-decided silence. n8n sendAndWait and Lindy set the closure bar behind 10.

Academia. CaMeL (provable injection security, ~7 utility points) and a ~24% baseline hijack rate for mail-reading agents frame HOMER's containment as the field's strong end. The 2026 memory literature (continuous LLM consolidation corrupts below no-memory; Letta's isolated writer concedes it) vindicates single-writer state. Alert-fatigue bars (~70% reliability crossover, ≥30% actionable, <10% demote) validate the quarantine. Mental-load research (~83% of anticipation/monitoring on one parent) makes 3 and 7 urgent.

## Ranked recommendations

1. **Paper-to-pipeline photo capture (local OCR, existing extractor).** Operators photograph paper flyers to the pipeline bot; images stay on-NAS, tesseract text rides the existing action-mail extractor into propose-tier previews; kill-switched, no new LLM site or slot. The one headline gap no wave touches; photo capture is market baseline (Ohai, Skylight, Alexa+); ends operator-as-scanner. M; cloud multimodal is a separate exposure decision.

2. **NEW / CHANGED / CARRIED day-delta stamps in the morning brief.** Code diffs today's payload against yesterday's per item id and stamps every printed line; pure presentation, everything prints, zero LLM. changedetection.io ships exactly this; Gemini's repetition is the counterproof. The delta engine already exists (lib/diff.py feeds the "Changes" section) — what's missing is the per-line stamp everywhere else, so carried items read as new every morning. Biggest daily reading cut. M; sequence after clusters arm.

3. **Full co-operator symmetry: Hermes access plus drafts to her Drafts (extends 1e).** Allowlist the co-operator on the Hermes bot, land her outgoing drafts in her own Fastmail Drafts, same consent tiers; opt-in, after 1e. Market best is visibility only (Maple read receipts); mental-load research says one-operator deployment re-entrenches the ~83% skew. Kills the relay in both directions. M; humans still send.

4. **Snapshot freshness contract: refuse stale answers (extends 3a).** Monotonic marker in the snapshot; Hermes refuses open-item/closure answers older than the newest outcome file. A closed camp re-offered three mornings from ~5-minute lag; lib/snapshot.py has no staleness check. Read-your-writes grounding; pipeline stays sole writer. S.

5. **/fewer: family-reachable per-class volume verb (batch to Sunday).** One tap moves an item class from daily print to the Sunday batch (batching, never suppression; /more reverses), stored via the state allowlist, applied by code. Gmail's volume threshold was its cheapest biggest win; FP/FN preferences are unlearnable from behavior. Both operators tune noise without SSH. M; flag the quarterly-adjust overlap in the PR.

6. **Evening-load collision detector (due-tomorrow × tonight's calendar).** Deterministic join of lms_due_tomorrow and tonight's events over a duration threshold; one morning line with a smallest next action; fires carry task ids; full 14-day quarantine. Reclaim's proven pattern; distinct from planned 1b/1d. Automates a join now done in the operator's head. M.

7. **Co-operator mailbox as an opt-in source (capture parity).** New Source-Contract JMAP module: school/household senders only, message-id dedup against the primary mailbox, own kill switch, her consent and credentials; supersedes the manual forward gate (a chosen shape, not a rejection). Mail only she receives is invisible today; Maple and Ohai show forwarding is the hand-run workaround. M.

8. **Task-id instrumentation so PPV covers the blind detectors.** Give conflicts, milestones, attendance, submissions, school-notes, and cross-check fires acknowledgeable ids joinable to outcomes, reusing existing button machinery, so PPV computes for all nine tokens and auto-demotion can engage. Gap #7, on no roadmap item; false alarms poison compliance system-wide. September's shakedown becomes a metrics read; the <50% bar untouched. M, school-start-gated.

9. **Strict-mode first-traffic window for the unverified parsers.** For ~14 days of real school traffic, paths that silently default empty (attendance, submissions, sports/lunch mail, the school-comms portal, the lunch-menu service) raise the source's flag naming the offending shape. Huginn's silent drops are the genre's canonical failure; summer drift announces itself in week one instead of masquerading as quiet. S; must land before school starts.

10. **Rollover [Today] and [Delegate] verbs in the reverse-handoff set.** Add task_commit_today and task_delegate to the closed verb set (absent from lib/reverse_handoff.py) under identical discipline: sanctioned emitter, id checks, ≤20/tick, still cannot express send; pipeline-first deploy. n8n sendAndWait and Lindy persist the human's answer; today these buttons are no-ops that resurface. S.

11. **Whole-source env kill switches for the core fetches (plus backup).** gradebook, LMS, email, calendar, backup get the contract-mandated switch: skip cleanly, carry forward, one code-stamped "(source off by operator)" line. The Source Contract mandates it verbatim; today the only off-path is credential removal, failing loud every run. Summer quiets stop masquerading as breakage. S.

12. **Deterministic outbound kid-PII filter on Telegram sends.** Post-narration check at the single send path against a state-sourced forbidden-strings list (surnames, school names, raw grade rows); on hit, deterministic renderer plus one flag. The transit mitigation is prompt-told, not code-filtered; Apple's warnings-not-fixes shows prompt guarantees fail. Hard floor on the one named accepted risk. S.

13. **Standing middleware drift check: cron plus backup excludes (extends Wave 4b).** Weekly midclt cronjob/cloud_backup dump, filtered to schedule/path/exclude fields (raw records embed live secrets), diffed against a committed manifest; drift flags. Bitten twice already, plus the middlewared loop death; protects the 06:00 heartbeat. S; front-runs Wave-4 sequencing, operator's call.

14. **One-line register-change disclosure in the next brief.** Any register or learned-rule change gets one code-composed line in the next brief, undo verb attached; demotion lines stay descriptions, never precision figures. Copilot Memory's visible "memory updated" contract is the one memory nicety HOMER lacks; wrongly-learned rules get caught the morning they land. S.

## Where we are vs where we can go

HOMER is not chasing the market; it owns everything vendors structurally cannot copy (conservation, earned silence, attention budget, quarantine, portal depth) at the bottom of a price band that just killed its commercial peers. The gaps are connective tissue, and the roadmap already covers most with stricter designs than anything shipped; this list is the residue. Capture leads as the one market-baseline hole, then the daily reading tax (2), three items that turn planned features from nominal to real (3, 4, 7), the proven cheapest win (5), and a September-gated governance cluster (8, 9, 11) that must land before school traffic or the shakedown stays manual. Parked deliberately: local narration, the ambient kid display, the Hermes spend ledger. Landed in order, HOMER becomes the only surveyed system that is connected, symmetric, and self-governing at once.

Sources: Ohai, Skylight, Hearth, Maple, Alexa+, Apple Intelligence/Siri, Gmail Priority Inbox, Shortwave, Slack AI Recap, Gemini Daily Brief, Reclaim, Motion, changedetection.io, Huginn, Khoj, OpenClaw, OpenJarvis, n8n, Lindy, Letta, Copilot Memory, CaMeL, Fitz 2019, Lee & See.
