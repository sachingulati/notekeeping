---
description: Check the store for what is broken, drifting, or worth doing. Reports; --fix repairs the unambiguous.
argument-hint: "[--fix] [--baseline] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git status:*), Bash(git log:*)
---

Check the store. **Reports by default.** `--fix` repairs only the unambiguous; nothing else writes.

**Under `--caller`: one line, and nothing else** - the counts go in the outcome line's detail, and
the findings below are not printed. `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: never ask - refuse naming the argument
that would satisfy it - and end with the outcome line.

Resolve the store first, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and check only
what is inside it. A store you found rather than resolved is the wrong store.

This command exists because **every failure the audit found was silent.**

| Severity | Means | `--fix` |
|---|---|---|
| **error** | broken now - something does not work | yes, repairs the unambiguous |
| **warn** | drifting - works, but degrading | no |
| **info** | an opportunity - a proposal you may decline | no |

## Errors

- The store resolves, exists, and is readable - naming the directories the walk actually covered.
- Each project resolves to a real directory.
- the store's `index.md` matches the folders on disk - no orphans, no dangling entries.
- Every work folder has a `requirements.md` with parseable frontmatter.
- A file declaring `authority: derived` that carries no refresh recipe.
- A projection that does not match its source, per
  `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`. Check both targets: `<repo>/CLAUDE.local.md`
  and `<workspace-root>/CLAUDE.local.md`. A projection whose flag is off is not a finding.
- Store `schema_version` newer than the installed plugin.
- A user-defined file definition with no `## Exclusion` section.
- A user command whose `writes:` declaration does not match what it touches, or that shadows a
  shipped command name.

The last two are overlay checks; resolve definitions through
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` so you are checking the definition this store
actually uses rather than the shipped default.

## Warnings

- **With `projections.enabled` on - the default** - a mapped repo whose `CLAUDE.local.md` is missing
  **or exists without our block**, or which has no `.git/info/exclude` entry. **A file that is
  present but carries no `notes:begin` is the same finding as an absent one** - the notes are not
  being delivered there either way. With the flag off, none of these is a finding. **`.gitignore` is
  never a finding** - the plugin does not write it (3.4).

  **Name the remedy that exists: `/nk:project <name>`, or `--fix`.** A projection is written when the
  project is *registered* and refreshed when it is rebuilt, so a missing one means it was deleted, or
  registered before the flag was on. **Do not tell the user the next save will write it** - a save
  maintains the active work item's project and nothing else, so for any other repo that is a remedy
  the user cannot perform.
- `NOTES.md` missing for an active project, or over budget.
- A knowledge file past `staleness_warn_commits` or `staleness_warn_days`.
- A memory item **this plugin wrote** surviving several saves unpromoted.
- **Not a finding:** content *outside* our block at either projection target - hand-written, or
  another tool's. That is expected and is never reported. We append below it and never touch it.
- An unresolved contradiction whose recorded check has not been run.
- A `requirements.md` amendment whose `Invalidates` names files nobody has revisited.

## Info

- The harness memory store growing unpromoted.
- A register past its split threshold - propose `areas/<topic>/`.
- The same fact in two projects - a workspace promotion, or a contract, per `depends_on`.
- A workspace or global entry whose provenance names one project - a demotion candidate.
- A `decisions.md` entry whose `Would reopen if:` trigger has plausibly fired.
- **Near-duplicate tags in `index.md`** - two tag values differing only by case, punctuation or
  plural, or obvious synonyms for one thread (`a11y` and `accessibility`). **Report both spellings
  with their item counts; never merge them, and this is never an error.** `/nk:save` may mint a tag
  from the session, so a variant can enter the store without anyone typing it - this is the repair
  path for that. Merging is a hand edit: only the user knows which spelling they meant, and `--fix`
  must not touch a label they chose.
- A work-item tree nested more than two deep.

## The baseline

`--baseline` records the current findings as acknowledged, so day-one noise does not drown day-two
findings. Write it to the store's `baseline.md`. Report new findings against it by default, and say how
many are baselined rather than hiding the count.

## Never

Report on anything outside the store. Everything outside `.notekeeping/` belongs to the user, and
will outlive this plugin; loose files, scratch, and anything the user put there are theirs.
