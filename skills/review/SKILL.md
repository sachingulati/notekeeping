---
name: review
description: Read the recorded knowledge - entries, not structure - and propose what to merge, split, retire, demote or promote; saves the report for --apply. Use when the user asks to prune, tidy, or review what has been recorded.
argument-hint: "[project] [--since <date>] [--apply [<run>] [all | <numbers>]] [--dry-run] [--page | --no-page]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact, ArtifactData
---

Read the store and propose what should change. **A pass with a finding is a run item** - its
report, its answers, its log - per `${CLAUDE_PLUGIN_ROOT}/reference/run-engine.md` (with
`${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md`, `${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md` and the
work definitions it names), and it is asked about once - *Apply / Publish as a page / Change answers
/ Not now* - per `${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`'s `## A yes applies what was
shown`. An answer in words - *"yes, but not 3"*, *"decline 5"*, *"3: merge into the auth entry"* -
re-enters this skill, is written onto the run's items, and is asked about again; nothing is applied
from the conversation. `--apply` applies a run - all of it, the findings you name, or one at a time
- in this session or any later one. Nothing is written without one or the other. Every question this skill asks - the scope
below, the report question, the walk - follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs. **Then the schema release**: read each resolved store's `schema_version` and `Grep` the release in `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (*The schema release this plugin ships*). A store at it is not mentioned; one behind it is said in one line on every run, naming `/nk:upgrade`, and the run carries on; newer or absent follows that file's four answers.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

**Read inside the resolved store**, plus the project's repository - per `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`, *Which repository* - when a finding needs a path checked against it - finding 5 is the
only one that does - and the store's own changed set under `--since`. Git runs only for those two, per
the repo-facts rule. Write nowhere else; everything outside `.notekeeping/` belongs to the user.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:review`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

**A re-entry? Decide before the scope and before any pass.** Where the conversation holds this
skill's report question with an answer typed after it, and the run that question named still has an
open finding, **there is no pass**: follow the re-entry in
`${CLAUDE_PLUGIN_ROOT}/skills/review/references/apply.md`. Otherwise the run is fresh.

## What this is, against `/nk:doctor`

| | `/nk:doctor` | this |
|---|---|---|
| Asks | *is it broken?* | *is it still good?* |
| Reads | structure, headers, paths | entry text - the content itself |
| Cost | cheap, run it often | a real pass; run it occasionally |
| Output | errors to fix | proposals to accept or decline |

Five of the ten findings below are also `doctor` findings. `doctor` sees them structurally and
cheaply - *this register is past its threshold* - and says so
continuously, so this command is never the first time you hear that something needs attention. What
it adds is the reading: which topic dominates the register, so which `areas/` the split creates.
A finding `doctor` can state, this command has to justify from the entries.

## Scope

With no scope the pass covers the whole store, which on a mature store is not something to run on
a whim - say how many files it will read and roughly what that costs before starting, then ask
whether to narrow it. With no turn to answer in, nothing is narrowed, and the pass covers the
whole store as resolved.

`[project]` limits the pass to one project's files.

`--since <date>` narrows the subject, costs less only where a bucket can be skipped, and switches
five findings off - read `${CLAUDE_PLUGIN_ROOT}/skills/review/references/since.md` before the pass,
and say what it says out loud before reading anything.

## The ten findings

Each one names what you read, not merely what you concluded. A proposal with no cited entries is
not a finding; say what you looked at. **Ten is what exists, not a quota** - a store with two findings
reports two.

| # | Finding | Proposal |
|---|---|---|
| 1 | Entries making the same claim, or each saying part of one thing | merge into one, keeping both corrections |
| 2 | A register past its split threshold, with a dominant topic | split into `areas/<topic>/` |
| 3 | Three or more entries on one topic across the scope's registers, with no area for it | create that topic's area |
| 4 | An entry marked `Unsettled` whose check is now cheap to run | propose running it - the check, verbatim |
| 5 | Entries about code that no longer exists - the path is gone | **retire** - mark superseded, never delete |
| 6 | A wide entry whose text names one project, or one workspace inside its own | demote to that project or workspace |
| 7 | The same fact in two projects | promote to the narrowest store enclosing both - the workspace, or one enclosing it - where it passes *Worth recording* |
| 8 | A `decisions.md` entry whose `Would reopen if:` has plausibly fired | **flag it for a human** - never reopened automatically |
| 9 | A work item with no session block for longer than `staleness_warn_days`, and no closing block | close it, or say why it is open |
| 10 | `NOTES.md` holding something needed on one task in ten | demote it to the file it belongs to |

**A finding's fingerprint** (the run engine's *Carried decisions*) is `<n> · <the files it names,
sorted>`, and its change part one text hash over the entries it names - each entry's text, sorted,
joined - so it comes back when an entry it names is edited, added or removed. **Finding 9's** is
`9 · <the item's id>`, its change part the date of the item's newest session block. **A finding
declined in an earlier review run is carried**, and the report lists it as such.

**Run items are not knowledge** - nothing here reads one, and finding 9 never proposes closing one.

Before reporting a finding, apply its test in `${CLAUDE_PLUGIN_ROOT}/skills/review/references/findings.md`
(with `${CLAUDE_PLUGIN_ROOT}/reference/content-tests.md`, `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`
for the `Unsettled` form and for findings 1 to 3 - *Organising as it writes* - `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/session.md` for finding 9,
and the scope's `areas-index.md` definition for 2 and 3). Where one entry earns two findings, rank
them per `${CLAUDE_PLUGIN_ROOT}/skills/review/references/overlap.md`.

## Report, and what a proposal looks like

Group by finding type, most confident first, a blank line between proposals. Each proposal carries:

```
<type> · <what you read>
  <the entries, quoted enough to judge>
  → <the proposal>
  why: <the rule above that it satisfies>
```

An applyable proposal names its write - the file, and what changes in it - because that line is
what *Apply* approves. A proposal that only says *merge these* has not shown what will be written.

Say what you read and found nothing in. A pass that reports four findings over 60 files should
say it read 60; otherwise a clean register is indistinguishable from one nobody looked at.

Number the findings as you print them, and the count is the last number - one list, nothing
reported outside it, carried decisions included, and any split into categories printed as arithmetic
that reconciles to it.
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`.

The report can be a page - the report question's *Publish as a page*, before anything is applied,
per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md` - which owns the offer, the two
flags, and what the page may carry. The terminal report is printed either way.

## `--apply`

A re-entry, *Apply*, and `--apply` on a run follow `${CLAUDE_PLUGIN_ROOT}/skills/review/references/apply.md`
(with `${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`, `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md` for an area or a merge, and the scope's `NOTES.md` and `areas-index.md`
definitions per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` for a split) - what is applyable, the walk, no turn to
answer in, and the rules under every form. Read it before the first write.
A write that leaves a file with a numeric `budget:` at or past `budget_notice_pct` of it says so, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`.
