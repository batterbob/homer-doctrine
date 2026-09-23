# HOMER repository map

**Status:** canonical active map, adopted 2026-08-20.

> **Note on this public edition.** The GitHub account, numeric repository IDs and
> NAS paths have been replaced with placeholders (`<org>`, `<nas-root>`). The
> structure and the reasoning are unchanged — and the structure is the point of
> the document, not the specific names.

Product names, repository identities, and runtime compatibility names are
different layers. GitHub slugs and NAS paths may change; the immutable
repository IDs and component roles below do not.

| Role | Product term | Canonical GitHub repository | Immutable identity | Current NAS path |
|---|---|---|---|---|
| Deterministic household application, PWA, and Daily Brief feature | HOMER | `<org>/homer-core` | numeric GitHub repository ID | `<nas-root>/dailybrief/` |
| Charter, decisions, audits, runbooks, prompts, and coordination | HOMER governance | `<org>/homer-governance` | numeric GitHub repository ID | `<nas-root>/homer/` |
| Privileged infrastructure control | HOMER Ops | `<org>/homer-ops` | repository-specific privileged boundary | `<nas-root>/homer-ops/` |
| Conversational LLM assistant | Hermes | local `hermes-agent` fork; deliberately not mirrored to GitHub | upstream lineage plus audited local commits | `<nas-root>/hermes-agent/` |

`dailybrief/` and `homer/` remain NAS compatibility paths during the migration.
They are not the product names. A later, separately audited runtime migration
may add canonical checkouts and compatibility symlinks.

## Coordination resources

Agents claim the logical aliases below. The coordination service stores them
by immutable repository ID or stable component role.

| User-facing alias | Canonical stored resource |
|---|---|
| `repo:homer-core:main` | `repo:github:<id>:refs/heads/main` |
| `repo:homer-governance:main` | `repo:github:<id>:refs/heads/main` |
| `nas:homer-core` | `nas:component:deterministic-app` |
| `nas:homer-governance` | `nas:component:governance` |

Legacy aliases remain valid and contend for the same leases:

- `repo:dailybrief:main` means the HOMER application repository.
- `repo:homer:main` means the governance repository, permanently.
- `nas:dailybrief` means the deterministic application component.
- `nas:homer` means the governance component.

Never reinterpret `repo:homer:main` as the application repository, and never
reuse the retired GitHub slugs `dailybrief` or `homer`. Slug reuse would destroy
GitHub's old-name redirect and could send a broad-credential stale clone to the
wrong repository.

## Terminology and compatibility

- **HOMER** is the deterministic household application.
- **Daily Brief** is a HOMER feature and output.
- **Hermes** is the conversational LLM assistant integrated with HOMER.
- **HOMER Ops** is the separately privileged infrastructure subsystem.
- **Hestia** is a historical codename only. Preserve it in dated evidence; do
  not use it in current identity, code comments, prompts, or user-facing copy.
- `dailybrief.env`, the application lock file, logger names, image aliases,
  and state filenames are internal compatibility interfaces. They change only
  for a functional reason under a separate runtime migration.
