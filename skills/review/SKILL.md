---
name: review
description: Read the recorded knowledge - entries, not structure - and propose what to merge, split, retire, demote or promote; saves the report for --apply. Use when the user asks to prune, tidy, or review what has been recorded.
argument-hint: "[project] [--since <date>] [--apply [<report>] [all | <numbers>]] [--dry-run] [--page | --no-page]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact
---

Read the store and propose what should change. Every proposal is saved as a report, and then
asked about once - *Apply / Apply and publish / Change answers / Not now* - per
`${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`'s `## A yes applies what was shown`. An answer in
words - *"yes, but not 3"*, *"3: merge into the auth entry"* - re-enters this skill, is written into
the report, and is asked about again; nothing is applied from the conversation. `--apply` applies
a saved report - all of it, the findings you name, or one at a time - in this session or any later
one. Nothing is written without one or the other. Every question this skill asks - the scope
below, the report question, the walk - follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs.
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
skill's report question with an answer typed after it, and the report that question named has no
`## Applied`, **there is no pass**: follow the re-entry in
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
not a finding; say what you looked at.

| # | Finding | Proposal |
|---|---|---|
| 1 | Two entries making the same claim from different sources | merge, keeping both provenance stamps |
| 2 | A register past its split threshold, with a dominant topic | split into `areas/<topic>/` |
| 3 | Three or more entries on one topic across the scope's registers, with no area for it | create that topic's area |
| 4 | An entry marked `Unsettled` whose check is now cheap to run | propose running it - the check, verbatim |
| 5 | Entries about code that no longer exists - the path is gone | **retire** - mark superseded, never delete |
| 6 | A wide entry whose provenance names one project | demote to that project |
| 7 | The same fact in two projects | promote to the workspace |
| 8 | A `decisions.md` entry whose `Would reopen if:` has plausibly fired | **flag it for a human** - never reopened automatically |
| 9 | A work item with no activity and no closure for months | close it, or say why it is open |
| 10 | `NOTES.md` holding something needed on one task in ten | demote it to the file it belongs to |

Before reporting a finding, apply its test in `${CLAUDE_PLUGIN_ROOT}/skills/review/references/findings.md`
(with `${CLAUDE_PLUGIN_ROOT}/reference/content-tests.md`, `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`
for the `Unsettled` form, `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/session.md` for finding 9,
and the scope's `areas-index.md` definition for 2 and 3). Where one entry earns two findings, rank
them per `${CLAUDE_PLUGIN_ROOT}/skills/review/references/overlap.md`.

## Report, and what a proposal looks like

Group by finding type, most confident first. Each proposal carries:

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
reported outside it, and any split into categories printed as arithmetic that reconciles to it.
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`.

The report can be a page. Where there is enough to choose between, offer it at the very
end, per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md` - which owns the offer, the two
flags, and what the page may carry. The terminal report is printed either way.

## `--apply`

A re-entry, *Apply*, and `--apply` on a saved report follow `${CLAUDE_PLUGIN_ROOT}/skills/review/references/apply.md`
(with `${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`, and the scope's `NOTES.md` and `areas-index.md`
definitions per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` for a split) - what is applyable, the walk, no turn to
answer in, and the rules under every form. Read it before the first write.
A write that leaves a file with a numeric `budget:` at or past `budget_notice_pct` of it says so, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`.

## Never

- **Never delete anything**, under any flag. Superseding is how this design retires a fact, in every
  file, and this command is not the exception.
- **Never rewrite an entry's claim** while merging. A merge joins two records of one fact; it does
  not restate the fact in your own words.
- **Never invent a finding to fill a category.** Ten types is what exists, not a quota - a store
  with two findings has two. **A proposal you cannot cite the entries for is a fabrication**, and
  this command reads the user's own knowledge, so a wrong proposal costs more than a missed one.
- Never report on anything outside the store, **beyond the path finding 5 verifies against the project's repository.**
- Never touch `requirements.md` bodies, which are write-once evidence, or `decisions.md` entries,
  which are append-only.
