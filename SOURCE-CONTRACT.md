# SOURCE-CONTRACT.md — the canonical text

Authored 2026-07-15. **This file is the sole authority.**

Every other place the contract appears — the kickoff's 6n block, `CLAUDE.md`, `SOURCES.md` —
holds a COPY. Copy it byte-for-byte. Never paraphrase it, never reconcile a copy against a
reconstruction, never "improve" it. If a copy and this file ever disagree, this file wins and
the copy is the bug.

Why a single file: on 2026-07-15 the image tag `dailybrief:expanded` was found in 26 places
across 8 files, having silently drifted from the `dailybrief:dev` the system actually runs —
and it broke the rollback in two separate documents. A string that appears in three places
drifts. This one has one home.

---

Every HOMER data source is a self-contained module obeying one contract: a fetcher retrieves raw data from the source (API, scrape, or IMAP/JMAP) and returns it unmodified; a normalizer translates that raw shape into the source's owned slice of the shared state model — the only place format knowledge lives, so a source's peculiarities never leak past this boundary; that normalizer also reports how many records it accepted and how many it rejected as unreadable — a record dropped by a deliberate policy filter is filtered, not rejected — with a reason code on every rejection, and a nonzero rejection count or an unrecognized nonempty payload classifies the source degraded rather than empty, because an empty slice is a claim about the source and only an empty source earns it; the normalizer runs inside per-source guards (a try/except that isolates this source's failure from every other run and every other source, per C12); on any failure, the source's prior slice is carried forward as the baseline rather than overwritten, and a source_flag announces the failure with a suggested fix, never silently; every source has an env kill switch that disables it without a rebuild; and if a source detects something (rather than just supplying data), its detector serves a mandatory PPV quarantine — logging fire/true-positive counts for 14 days, log-only, before it may notify a human. Nothing downstream of a source's owned slice may know or depend on where that data came from, how it was fetched, or what format it arrived in — the slice's schema is the only interface the rest of the system sees.

---

## Copies to keep in sync

| Location | Status |
|---|---|
| `homer/SOURCE-CONTRACT.md` | **canonical — this file** |
| kickoff block 6n | copy (fold in at step 6b) |
| `CLAUDE.md` | copy (created during the build) |
| `SOURCES.md` | copy (created by 6n, after the build, generated FROM final code) |

Verify with: `grep -c "Every HOMER data source is a self-contained module" <file>`
