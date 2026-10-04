---
name: budget
description: Show what the recorded notes cost in context - always loaded, read on demand, and registers - per scope and file. Use when the user asks how much context the notes use or what they cost.
argument-hint: "[project] [--all] [--oneline]"
allowed-tools: Read, Glob, Grep
---

Report what is actually being spent. **Reads only - this command writes nothing.**

Before anything else, in order:
1. **`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
2. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`.
3. **An overlay?** If `<store>/schema/skills/budget/` exists, follow `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`: a `SKILL.md` there replaces the rest of this file, and a file under its `references/` replaces the shipped reference of that name wherever this skill cites it.
4. **Infer before asking**: read what the conversation already states, and ask only what is still open.

This command only reads, so it behaves normally under `--oneline` - compress the figures into the outcome line's detail.

What is
measured is the store, plus the three read-line targets and the always-loaded content they sit beside
- that is the whole question this command answers, and all of it is a file this plugin wrote or a
file already being charged. Nothing else outside the store is read. The targets are defined in
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`) - read which exist
there rather than assuming.

Every byte figure is an estimate. Nothing granted here returns an exact byte count, so a size is
read from the text and rounded. Entry counts, below, are exact.

## Three sections

**Always loaded** - charged on every prompt. Each scope costs its read line plus what the read pulls
in: `NOTES.md`, and what `NOTES.md` reads on to - `instructions.md` and `areas/INDEX.md`. For global
it is `~/.claude/rules/notekeeping.md` and the files it imports. `NOTES.md` still counts as
always loaded, because the read happens. No total line: a sum across the scopes
is a number the reader can see for themselves. A scope whose file does not exist is reported as
absent, not as zero.

For scale, in the same session - the repository's committed instructions file, and the
harness's own always-loaded memory index where its path is known. Name those numbers beside the
plugin's; report a missing one as unmeasured rather than as zero.

**Source documents** - read on demand, not charged unless read. Every definition whose resolved
`budget:` is a byte ceiling, at each scope in reach: the `NOTES.md` files, the `instructions.md`
files, global's `environment.md`, `overview.md`, and the active work item's `summary.md` where one
exists. Derive the set from
the definitions rather than from this sentence - an overlay can add a budget, and the row it adds
belongs here.

**Registers** - no cap; the thresholds are proposals. Show size and entry count - counted with
`Grep`, never by reading the file whole - against the split threshold, marked `proposed` on the
row itself.

## Always name the window

A share of context is only meaningful against a window: the same store is one percentage of a small
window and a much smaller one of a large window. Report the value of `context_window_tokens` that
was used.

**Read that value; never assume one.** Resolution order and the shipped default are in
`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`. Reporting against the wrong window silently
misstates every percentage in the section - which is why the window is printed as its own line
before any percentage exists, rather than trusted to be remembered while the sections are written.

## The shape

Filled from the run, never copied from here. Nothing below is a value; every `<slot>` is
something this run measured.

Four columns, the same four in every section: what it is, how full it is, the measurement it
came from, and what it costs against the window.

```
Budget - <store scope>
Window: <context_window_tokens> tokens (<where that value came from>)

  File                Full     Used / limit                  Share of window

Always loaded
  <path>              <pct>%   <used> / <ceiling>            <ctx pct>%
  <path>              <pct>%   <used> / <ceiling>            <ctx pct>%

For scale
  <path>               -       <bytes> / -                   <ctx pct>%
  <what>               -       unmeasured - <why>             -

Source documents
  <path>              <pct>%   <used> / <budget>             <ctx pct>%

Registers
  <path>              <pct>%   <n> / <threshold> proposed    <ctx pct>%
```

The column header is printed once, under the window line, and names all four columns - *File*,
*Full* (used as a share of its own limit), *Used / limit* (bytes, or entries against a proposed
threshold for a register), *Share of window*. Two percentage columns side by side are unreadable
without it: one is against the file's own limit, the other against the context window.

`<ctx pct>` is always the share of the window named on line 2 - the configured value, never a
hardcoded one.

A column that does not apply carries `-`, never a blank and never a zero. A file with no ceiling
is not a file at 0%.

The window line is printed before the sections, and no percentage may be printed before it. A
denominator stated after the figures it divides is a denominator nobody checks; stated first, it is
a slot that is visibly empty when it was never resolved.

The order of the sections is fixed and the slots are not optional - a row with nothing to put in
one carries the marker for that, `unmeasured` or an empty render, and never silence.

## Forms

| Form | Does |
|---|---|
| `/nk:budget` | every budgeted file for the current project, plus workspace and global |
| `/nk:budget <project>` | one project |
| `/nk:budget --all` | every project in the store |

Over budget is a `warn` from `/nk:doctor`, not an error here. This command answers *how much am I
using*, not *what is broken*.
