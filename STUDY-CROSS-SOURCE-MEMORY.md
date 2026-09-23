# Study: cross-source, cross-day duplicate memory

**Commissioned by:** Claude-PM  
**Code watermark:** `<org>/homer-core@0097f72db96f705bab4027c4eaeafaeaaaef7ad7`  
**Nature of work:** study and recommendation only; no implementation or configuration change  
**Privacy:** every example below is invented. No real child, school, teacher, or message text is used.

## Recommendation

Do **not** build general semantic, invisible cross-day suppression. Build a deliberately narrow, deterministic identity matcher for exact replicas and shared canonical objects; when it fires, render a short code-generated **repeat stub** in the existing brief and retain a durable receipt, so the new occurrence is compact but never silent. Run every fuzzy method and any LLM judgment in shadow only; they may generate candidates and explanations, but they must not authorize a drop.

This leaves value on the table: paraphrased duplicates will still appear, and even an exact repeat will occupy one compact line. That is the right first cost because the asymmetric error is not “some extra prose”; it is hiding the new notice that silence was supposed to protect.

There is also a current-tree correction PM needs before treating the baseline as settled: non-calendar source-ID memories are private, but school-note outputs already have a shared cross-day memory that the **model** is instructed to use suppressively. That is not a deterministic cross-source matcher, and it is not protected by the narration cluster conservation mechanism.

## Scope, method, and limits

I read `CLAUDE.md` in full before following the relevant call paths. The binding charter says detection is deterministic, model clustering is phrasing only, and dropping remains code-owned (`CLAUDE.md:9-21`); it also makes silence an earned condition and budgets attention at no more than four context-switches per day (`CLAUDE.md:15-18`, `CLAUDE.md:43-56`).

The reviewed set was derived as follows:

1. I started with every supplier named in the commission: school email, school news, morning announcements, the school-comms portal, gradebook-portal documents, and paper photos.
2. I followed each supplier's candidate and seen-state path into `brief.build_payload`, `_school_notes`, or the action-mail extractor (`brief.py:1363-1427`, `brief.py:1521-1629`, `brief.py:2440-2564`, `brief.py:3067-3206`).
3. I searched the immutable indexed tree for `already_known`, `*_seen`, `dedup`, `remember`, `call_extractor`, cluster functions, state writes, and receipt paths, then read the matching functions and their callers. The four extractor call occurrences were independently enumerated from `model.call_extractor` in `brief.py:2354-2359`, `brief.py:3179-3181`, `brief.py:3260-3263`, and `brief.py:3308-3309`.

That derivation can miss behavior created by dynamic dispatch without those spellings, a downstream service outside this repository, and anything present only in a live deployment. I did not inspect live `briefs/`, live messages, live environment overrides, or any PII. Therefore this study cannot determine the real duplicate arrival rate, the distribution of delay or paraphrase distance, the deployed value of `NARRATION_CLUSTERS`, the live backup posture, or the false-match rate of any proposed threshold. The repository itself identifies `briefs/` as PII and says it cannot prove live backup posture (`CLAUDE.md:109-117`); the ignore rule covers the entire directory (`.gitignore:1-4`). Those unknowns require a privacy-preserving shadow corpus and human labels, not confidence inferred from source code.

## Verified current behavior

### 1. Calendar-event correlation is real, cross-day, and event-date anchored

PM's substance is correct, with one locator correction.

The title matcher lowercases, deletes punctuation, removes a short stopword list, and compares token sets with Jaccard (`lib/eventverify.py:45-49`, `lib/eventverify.py:83-103`). `EVENT_MATCH_MIN` defaults to `0.6` (`config.py:284`). Kid compatibility rejects two different named kids but treats an unattributed side as non-contradictory (`lib/eventverify.py:285-298`).

The first guard is the exact proposed-event key: `brief.py` builds the set of prior keys and removes a proposal whose key is already present (`brief.py:1160-1169`). Code then calls `dedup_against_history` on the pre-append history (`brief.py:1170-1174`). That second guard requires the proposed event's `date` to equal the history record's date, kid compatibility to hold, and title Jaccard to meet the threshold (`lib/eventverify.py:437-469`). A re-announcement with a genuinely different event date is therefore retained by construction (`lib/eventverify.py:438-443`).

There is no arrival-time predicate in that history comparison. The stored record carries the event's date, and the comparison is `p.date == history.when` (`lib/eventverify.py:448-465`), so a delayed reminder can still match. Retention is bounded by volume rather than elapsed time: event proposals are appended as `{key, kid, date}` and sliced to the last 100 (`brief.py:1183-1185`). PM's cited `brief.py:1229` and `brief.py:1912` are also real 100-record caps, but those two occurrences maintain the same shared slice while adding travel-leg records (`brief.py:1214-1229`, `brief.py:1891-1912`); the proposal append itself is at `brief.py:1183-1185`.

The consequence is worth stating precisely. Calendar correlation survives delay **while its record remains among the last 100 entries**. The tree does not prove how many days that represents under live traffic.

### 2. Non-calendar source-ID memories are private, but “no cross-source matching whatsoever” is false

The private-memory half is verified:

- School news diffs WordPress post IDs against its own seen list and caps that FIFO at 200 (`lib/schoolnews.py:21-24`, `lib/schoolnews.py:76-79`, `lib/schoolnews.py:102-110`).
- Morning announcements do the same with their own IDs and a 300-entry cap (`lib/schoolannounce.py:35-39`, `lib/schoolannounce.py:95-98`, `lib/schoolannounce.py:121-129`).
- the school-comms portal diffs its own post IDs and caps its own FIFO at 200 (`lib/schoolcomms.py:31-34`, `lib/schoolcomms.py:173-176`, `lib/schoolcomms.py:200-208`).
- gradebook-portal documents diff `documentGU` values and cap their own FIFO at 200 (`lib/pvdocs.py:52-56`, `lib/pvdocs.py:149-152`, `lib/pvdocs.py:247-255`).
- Photos remember Telegram `file_unique_id` values in a separate 200-entry FIFO and stage their own pending pseudo-emails (`lib/photos.py:37-48`, `lib/photos.py:236-254`).

The state model carries those as separate slices (`brief.py:827-850`, `brief.py:873-887`), and the full-state rewrite persists them separately (`brief.py:7715-7744`). None of those source-ID sets can equate an email ID with a portal-post ID.

However, school email, school news, the school-comms portal, morning announcements, and gradebook-portal document candidates are pooled into one school-notes extraction pass (`brief.py:1521-1536`). After that pass, code normalizes every surfaced note line to lowercase/collapsed whitespace and remembers up to 100 `{title_norm, date_seen}` records in the shared `school_notes_seen` slice (`lib/schoolnotes.py:183-221`; `brief.py:1624-1629`). On the next run, up to 40 known titles are placed in the extractor payload as `already_known` (`lib/schoolnotes.py:175-180`; `brief.py:3171-3181`). The prompt explicitly tells the model to exclude anything “substantially matching” those titles (`prompts/schoolnotes.txt:3-6`, `prompts/schoolnotes.txt:15-23`).

Code validates that the extractor returned a list, an allowed kid label, nonempty text, a parseable optional date, and an offered source ID; it does **not** compare an emitted or omitted item with `already_known` (`lib/schoolnotes.py:129-172`). Thus the current tree already permits a model to suppress a cross-source, cross-day school-note item, but the match is probabilistic, the omission has no pair-level receipt, and no conservation check proves what was omitted. The memory is also lossy twice: it retains 100 records but exposes only the sorted first 40 to the model (`lib/schoolnotes.py:175-180`, `lib/schoolnotes.py:183-221`).

There are narrower exceptions. gradebook-portal documents receive a deterministic floor note when the extractor does not cover them (`brief.py:1612-1623`), so their existence does not rely solely on the model. Photos ride the action-mail extractor rather than `school_notes_seen` (`lib/photos.py:5-20`, `lib/photos.py:257-264`). The accurate baseline is therefore:

> There is no general **deterministic** non-calendar cross-source matcher. There is already an unreceipted, model-directed cross-day suppression hint for the shared school-notes lane, while each transport separately remembers only its own source IDs.

That is a substantive correction to PM's premise, not merely documentation drift.

### 3. Same-brief narration clustering is verified and is not a cross-day precedent for loss

`NARRATION_CLUSTERS` defaults to `"0"`; `shadow` validates and records proposals without changing output, and `"1"` renders accepted folds (`config.py:96-110`, `lib/model.py:180-188`). The clustering allowlist contains only `cal`, `note`, `mail`, and `prop`; flags, tasks, bills, decisions, sickness, wins, and grades are excluded (`lib/model.py:1018-1036`).

For each proposed cluster, `_clean_clusters` applies checks in a fixed order and records the first failure reason; an invalid proposal is rejected whole (`lib/model.py:1067-1158`). `_conserved` then requires each offered ID to occupy exactly one role—individual item, fold host, or member of one fold—and a failure discards every accepted cluster for that run (`lib/model.py:1198-1225`, `lib/model.py:1418-1434`). Rendering derives the host from the current plan, keeps action links, and falls back to each member's deterministic line when it is not validly folded (`lib/model.py:1161-1195`, `lib/model.py:1264-1336`).

Receipts are appended to `briefs/cluster-receipts.jsonl` and pruned to 30 days in the same rewrite (`lib/model.py:1228-1261`); the non-dry-run caller writes them (`brief.py:7132-7138`). The scope is one current `plan`: `render_items` builds it from the current payload and offers current item IDs in the one narration call (`lib/model.py:1381-1410`). Nothing from yesterday must disappear for today's fold to work. That is precisely what changes across days.

## 1. Can pure deterministic code do this well enough?

**Yes for a high-precision subset; no for the whole semantic class.** Deterministic code can safely identify exact replicas and shared canonical objects. It cannot, at acceptable recall and near-zero false-fold risk, decide every paraphrased school communication using surface text alone.

The techniques have different appropriate roles:

| Technique | What it catches | What it misses | What it falsely matches | Safe role |
|---|---|---|---|---|
| Normalized full-text fingerprint | The same notice copied from a portal into email with HTML tags, Unicode punctuation, tracking parameters, and whitespace changed. Invented example: “Early release on 14 October at 1:15 p.m.” appears as HTML in one channel and plain text in another. | Summaries, paraphrases, OCR errors, a newsletter excerpt versus a full post. | Recurring boilerplate if normalization deletes the only distinguishing date, URL, number, or negation. Invented example: two monthly “permission form due Friday” notices collapse if “Friday” is discarded. | Auto-compaction only when normalization preserves every number, date, time, negation, destination, and canonical link; use a keyed fingerprint, not raw SHA. |
| Word shingling / MinHash | Long notices with paragraphs reordered, a different greeting/footer, or a small added preface. | Short alerts, heavy paraphrases, OCR substitutions, and a post that summarizes a PDF. | Weekly newsletters whose template dominates the content; two different field-trip forms with nearly identical legal paragraphs. | Candidate generation or shadow scoring, never the final drop predicate. |
| Token Jaccard | Reordered wording with substantially the same vocabulary. The calendar path demonstrates the transparent mechanics (`lib/eventverify.py:83-103`). | Synonyms such as “dismissal” versus “release,” abbreviations, and short texts where one changed token moves the score sharply. | Short generic notices: “band concert rescheduled” can describe two dates or two student groups. | Candidate generation; final eligibility only with strong, equal fact anchors and no conflict. |
| TF-IDF / cosine | Shared rare terms despite common boilerplate, such as an unusual program noun plus a specific venue phrase. | Very short messages and semantically equal wording with few shared terms. IDF also drifts as the corpus changes. | Separate notices in the same rare program: a rehearsal change and a performance change can look closer to each other than either looks to a generic template. | Ranked retrieval in shadow. Persist the model/version of the corpus statistics if it is ever used for audit. |
| Deterministic entity/fact extraction | Equivalent wording that states the same explicit event date, deadline, time, audience slot, action noun, location, and document link. It also supplies strong vetoes: a new date or new deadline means “not duplicate.” | Relative dates (“next Friday”), implicit audiences, negation, multi-item newsletters, OCR errors, and nouns outside a maintained vocabulary. | Two distinct events for the same audience on the same day; two forms with the same deadline; an announcement and a reminder that adds a required action. | Mandatory anchors and vetoes, not a similarity engine by itself. Unknown or conflicting facts must fail open. |
| Source-pair heuristics | An email and portal post sharing the same canonical post URL/object ID; a news item and email linking the same PDF; a portal notification wrapper around one stable object. | Forwarded copies, rewritten notices, screenshots, and channels that omit the original link. | A daily digest linking several objects, or two notices linking the same general calendar/home page. | Strong identity proof only when the shared identifier names one object, not a collection or landing page. |

The crucial distinction is **candidate** versus **eligibility**. MinHash, Jaccard, and TF-IDF can cheaply find pairs a human or shadow evaluator should inspect. They do not prove identity. A safe deterministic production gate should accept only one of these identity predicates:

1. the two source records resolve to the same stable canonical object or document; or
2. their complete, conservatively normalized content fingerprints are equal **and** every explicit fact agrees **and** neither side contains a fact absent from the other.

Any parser uncertainty, missing anchor on a short notice, changed date/time/audience/action/link, added negation, or failed memory/receipt write must render the current item normally. That will miss many true paraphrases. It is still “well enough” for the first increment if the claim is honestly limited to provable replicas, not sold as semantic deduplication.

## 2. Would an LLM do it better, and where exactly?

**Plain answer: an LLM is good at recognizing semantic duplicates, and it will outperform lexical rules on paraphrases. It is not reliable enough to own an invisible suppression decision.**

The largest benefit is **ambiguous-pair adjudication**. Given two short, bounded records, a capable model can recognize that “students follow the two-hour release schedule” and “dismissal moves up by two hours” describe the same event, or that a second notice adds a new pickup instruction and is therefore not a duplicate. It is also strong at explaining the evidence to a human: “same date and dismissal time; the portal copy adds no action.”

The benefit is smaller for **candidate generation**. Deterministic inverted indexes, keyed shingles, and exact canonical links can retrieve a small candidate set more cheaply, reproducibly, and without another disclosure of message content. A model or embedding search can improve recall, but candidate recall does not justify a new production call site when fuzzy candidates may remain shadow-only.

The risks concentrate exactly where this system is least tolerant: changed dates, negation, an added required action, two children with parallel notices, and prompt-like text inside untrusted school content. The charter says email content is data, never instructions (`CLAUDE.md:208-214`). A pair classifier would need the same sanitization and prompt-injection discipline, yet even a well-contained call remains stochastic and can be confidently wrong.

Accordingly, the safe LLM roles are:

- shadow adjudication to estimate how much value deterministic exact matching leaves behind;
- a proposed match reason that code records but does not act on; and
- human-facing explanation after code has independently established identity.

The unsafe role is “model says same, therefore today's item is absent.”

## 3. LLM involvement and HARD RULE 1

The cluster pattern extends only halfway. A model may propose a pair, and code may validate structural invariants, write a receipt, and fail open. But for a drop, code must independently prove the identity predicate; validating that the model returned two real IDs, equal extracted dates, and a reason string is not the same as validating semantic equivalence.

Same-brief conservation proves that every current offered ID still has a rendering role (`lib/model.py:1198-1225`). A cross-day “conservation” check can prove only a bookkeeping identity—for example, every current item is either fully rendered or represented by a repeat stub and receipt referencing a prior delivered record. It cannot prove that the prior record meant the same thing. The semantic decision therefore remains the dangerous step.

I do **not** recommend bending HARD RULE 1. If PM chose model-adjudicated silent drops, the charter would need an explicit amendment saying so; describing the model as merely “proposing” would not make the model's semantic verdict code-owned. The existing four extractor calls and single narration touchpoint are intentional constraints (`CLAUDE.md:101`; the four concrete occurrences are `brief.py:2354-2359`, `brief.py:3179-3181`, `brief.py:3260-3263`, and `brief.py:3308-3309`). Narration already uses the one current-payload call (`brief.py:7091-7142`). A fifth call dedicated to pair decisions would be a significant architectural change even if its output were shadow-only; for production suppression it would also violate the rule's substance.

The discovered `school_notes_seen` path deserves explicit treatment. Its prompt asks the model to omit substantial matches (`prompts/schoolnotes.txt:15-23`), while code validates output shape but not duplicate equivalence (`lib/schoolnotes.py:129-180`). The charter permits validated add-only extractors, but it separately says dropping stays in code (`CLAUDE.md:13-15`). On the evidence available, this is at minimum an unresolved rule-boundary ambiguity and at worst an existing violation. It should not be cited as precedent for expanding model-owned suppression; PM should route it for a focused disposition.

## 4. Failure asymmetry: the controlling requirement

A false split costs one repeated line inside an already scheduled message. A false fold can conceal a changed time, a new form, a new audience, or a cancellation. The safe failure direction is therefore unequivocal: **show the item**.

For an **invisible** automatic drop, the tolerable false-positive rate is **0% as a design budget**. No empirical classifier can prove a true population rate of zero. As a release-evidence floor, zero false folds in 3,000 human-adjudicated would put the rough one-sided 95% “rule of three” upper bound near 0.1%; that is evidence, not a guarantee, and the repository contains no live volume from which to translate even 0.1% into annual household harm. The threshold should not be relaxed merely because the sample is hard to obtain.

The mechanism that guarantees the safe direction must be structural, not aspirational:

1. **Fail-open gate:** no candidate, parser error, memory error, receipt error, unknown fact, or disagreement can suppress or compact; it renders fully.
2. **Novel-fact veto:** a new or changed absolute date, time, audience slot, action requirement, negation/cancellation marker, location, or canonical document link forces a full render. Same date is required just as the event history matcher requires it (`lib/eventverify.py:437-469`).
3. **Current-run representation invariant:** every accepted input item must be either fully rendered or represented today by a code-generated repeat stub. There is no “receipt only” outcome.
4. **Durable-before-compact receipt:** the compact path is eligible only after its receipt is atomically durable. If that write fails, render fully. This is stronger than cluster receipts, whose best-effort loss is safe only because cluster members remain conserved in the brief (`lib/model.py:1228-1261`).
5. **Source access:** the repeat stub keeps the current source link/action marker where one exists. A user can open today's source without hunting for the prior brief.
6. **Bounded shadow evidence:** fuzzy candidates never affect rendering. Their labeled receipts measure false splits and false folds before any future scope decision.

A compacted item should remain visible in two places:

- in the brief, as a terse line such as “Repeat from portal: early-release notice for 14 October; no new facts” (invented); and
- in `briefs/cross-source-receipts.jsonl`, with the full deterministic brief line, current and prior source references, the identity reason, fact comparison, and run IDs.

That is not a perfect duplicate “drop”; it is a lossless attention reduction. It guarantees that a mistaken fold cannot turn today's occurrence into silence. If PM requires zero line at all, no fuzzy or LLM path can meet the required safety guarantee.

## 5. What the memory should be

### Working index: `briefs/state.json`

Add one bounded pipeline-owned top-level slice, conceptually:

```json
{
  "cross_source_memory": {
    "version": 1,
    "records": [
      {
        "delivered_at": "UTC timestamp",
        "expires_at": "UTC timestamp",
        "source_kind": "email|schoolcomms|schoolnews|schoolannounce",
        "source_ref_hmac": "HMAC-SHA-256",
        "content_hmac": "HMAC-SHA-256",
        "canonical_object_hmacs": ["HMAC-SHA-256"],
        "facts": {
          "event_date": "YYYY-MM-DD or null",
          "deadline_date": "YYYY-MM-DD or null",
          "time": "normalized explicit time or null",
          "audience_slot": "configured opaque slot or null",
          "action_kind": "closed code enum or null",
          "negation": false
        },
        "run_id": "prior delivered run identifier",
        "item_id": "prior code-minted item identifier"
      }
    ]
  }
}
```

The state record stores no raw body, OCR text, attachment, school name, teacher name, child name, free-text title, or URL query. Use HMAC-SHA-256 with a dedicated secret outside `state.json`; an ordinary SHA of predictable notices is dictionary-testable. Secret absence or rotation invalidates matching and therefore produces duplicates, the safe direction. The `facts` object should use closed enums and opaque configured audience slots rather than names.

Retention should be dual-bounded:

- undated records: 45 days;
- records with an explicit event/deadline date: through seven days after that date, but never more than 120 days after delivery; and
- an absolute cap of 500 records, evicting expired first and then oldest delivered.

Forty-five days aligns with the existing event-verification horizon (`lib/eventverify.py:42-43`) while allowing notices separated by weeks. The 120-day and 500-record limits are proposed engineering bounds, **not values established by live traffic**; shadow receipts must show whether they cover the observed delay distribution. Eviction causes a duplicate, never hidden content.

`state.json` is the right read-optimized rolling working set. It already has atomic replacement and a rolling valid backup (`lib/state.py:291-326`), and the charter permits only the pipeline to write it (`CLAUDE.md:20`, `CLAUDE.md:62-100`). A future implementation would have to register the new key in the existing state-writer/schema controls; `update_state_keys` has no runtime allowlist (`CLAUDE.md:62-100`). The new memory should be added only after a successful delivery, so a processed-but-undelivered item never becomes evidence that the family saw it.

### Audit and recovery: a separate bounded receipt file

Use `briefs/cross-source-receipts.jsonl`, not `events.jsonl` and not the cluster receipt file. Each line should contain:

- decision: `shadow_candidate`, `full_render`, or `repeat_stub`;
- deterministic reason code (`same_object`, `same_full_content`, or a rejection/veto reason);
- prior/current run and item IDs;
- prior/current source kinds and recoverable opaque source IDs;
- compared facts and any fact delta;
- the current deterministic brief line and the emitted repeat stub; and
- matcher/schema version.

The deterministic line is PII, but it is the minimum human-recoverable content and lives inside the already git-ignored PII boundary (`CLAUDE.md:109-117`, `.gitignore:1-4`). Do not store raw bodies, attachments, OCR, token lists, or unkeyed shingles. Prune receipts after 30 days, matching the operational review horizon already used for cluster evidence (`lib/model.py:1228-1259`). If a future evaluation needs longer aggregate evidence, export only counts by reason/version—never message content—after human labels are complete.

`events.jsonl` is the wrong store. It is the durable action ledger, read across current and rotated files (`lib/events.py:15-64`), rotates monthly without deleting the archives (`lib/events.py:85-97`), and fsyncs applied/duplicate action records (`lib/events.py:100-145`). Cross-source comparison receipts are higher-volume, contain PII, and have a different retention/deletion contract. Reusing that ledger would mix “what the household changed” with “why a line was compacted” and make privacy expiry harder.

The cluster receipt schema is a useful pattern but not a safe sink to reuse. Cluster receipts are best-effort because a missing receipt cannot erase a member; a cross-day compaction receipt is part of the recoverability invariant and must fail the decision open (`lib/model.py:1228-1261`).

## 6. Smallest viable version

Authorize one increment with this exact boundary:

1. Cover only the four text transports that already share the school-notes lane: school email, the school-comms portal, school news, and morning announcements (`brief.py:1363-1410`, `brief.py:1521-1536`). Do not include gradebook-portal documents, which already have a deterministic floor, or photos, which use a different extractor path (`brief.py:1612-1623`, `lib/photos.py:257-264`).
2. Create the bounded state index and durable 30-day receipt file above.
3. Match only across **different** source kinds and only on a shared single-object canonical identifier or equal conservative full-content HMAC plus equal explicit facts and no novel-fact veto.
4. Emit a code-generated repeat stub, retaining today's source link, rather than making the item invisible.
5. Record MinHash/Jaccard/TF-IDF candidates in shadow if inexpensive, but let none affect output.
6. Do not add an LLM call. Do not let the existing school-notes model decide the new suppression. The current `already_known` omission path should receive a separate explicit disposition; the increment must not quietly depend on it.

Deliberately **do not** build semantic paraphrase suppression, embeddings, a fifth LLM call, model adjudication, a learned threshold, shared memory for every source, indefinite history, raw-content storage, or a new notification. Those additions increase surface area before the household has evidence about actual source pairs and delay distributions.

This increment will not solve the entire complaint. It will remove exact mirrors and canonical-object wrappers, make every decision inspectable, produce the corpus needed to size the remaining semantic gap, and preserve the safe outcome on every failure. Only after shadow labels show a valuable, tightly bounded class beyond exact identity should PM consider widening a deterministic predicate; an LLM may explain those cases, but should still not be allowed to silence them.

## Final disposition

**Recommend:** deterministic identity-only cross-source memory, visible repeat stubs, durable receipts, and fail-open behavior. **Reject for now:** invisible fuzzy or LLM-decided suppression. **Open current-tree issue:** `school_notes_seen` already asks the extractor model to omit substantial cross-day matches without code validation or conservation; PM should decide whether that conforms to the add-only extractor exception before using it as precedent.
