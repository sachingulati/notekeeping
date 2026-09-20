---
description: Show what the notes are actually spending - always-loaded, on-demand, and registers.
argument-hint: "[project] [--all] [--caller <name>]"
allowed-tools: Read, Glob
---

Report what is actually being spent. **Reads only - this command writes nothing.**

**`--caller <name>`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`. This command
only reads, so it behaves normally - compress the figures into the outcome line's detail.

Resolve the store first, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. **What is
measured is the store, plus the two projection files and the always-loaded content they sit beside**
- that is the whole question this command answers, and all of it is a file this plugin wrote or a
file already being charged. Nothing else outside the store is read. The projections and their
ceilings are defined in
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - read which targets exist and what bounds them
there rather than assuming either.

Budgets are configurable, but a ceiling you cannot see yourself approaching is one you only meet as a
warning after the fact.

## Three sections, and the split is the point

**Always loaded** - charged on every prompt. Both projections - `<repo>/CLAUDE.local.md` and
`<workspace-root>/CLAUDE.local.md`. **No total line**: a sum across two rows is a number the reader
can see for themselves, and it was the one figure in this report with nothing to check it against.
**A projection with nothing to render is reported as an empty
render, not as a gap**: a source with no content is a different fact from a delivery that failed.

**For scale, in the same session** - the repository's committed instructions file, and the
harness's own always-loaded memory index **where its path is known**. Name those numbers beside the
plugin's; report a missing one as unmeasured rather than as zero. The point is honesty about what
this costs relative to what is already there.

**Source documents** - read on demand, not charged unless read. `NOTES.md` per project, the
workspace's own `NOTES.md`, `~/.notekeeping/NOTES.md`, `overview.md`.

**Registers** - no cap; the thresholds are proposals. Show size and entry count against the split
threshold, and **mark the threshold as proposed on the row itself**: the percentage beside it would
otherwise read as a limit, and a register past it is doing nothing wrong.

## Always name the window

A share of context is only meaningful against a window: the same store is one percentage of a small
window and a much smaller one of a large window. **Report the value of `context_window_tokens` that
was used.**

**Read that value; never assume one.** Resolution order and the shipped default are in
`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`. Reporting against the wrong window silently
misstates every percentage in the section - which is why the window is printed as its own line
before any percentage exists, rather than trusted to be remembered while the sections are written.

## The shape

**Filled from the run, never copied from here.** Nothing below is a value; every `<slot>` is
something this run measured.

**Four columns, the same four in every section:** what it is, how full it is, the measurement it
came from, and what it costs against the window.

```
Budget - <store scope>
Window: <context_window_tokens> tokens (<where that value came from>)

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

**`<ctx pct>` is the share of the window named on line 2, not of a fixed million.** The window is a
setting; a column that hardcoded one value would be wrong for every store that changed it, which is
the same failure as naming the wrong window in the first place.

**A column that does not apply carries `-`, never a blank and never a zero.** A file with no ceiling
is not a file at 0%.

**The window line is printed before the sections, and no percentage may be printed before it.** A
denominator stated after the figures it divides is a denominator nobody checks; stated first, it is
a slot that is visibly empty when it was never resolved.

**The order of the sections is fixed and the slots are not optional** - a row with nothing to put in
one carries the marker for that, `unmeasured` or an empty render, and never silence.

## Forms

| Form | Does |
|---|---|
| `/nk:budget` | every budgeted file for the current project, plus global |
| `/nk:budget <project>` | one project |
| `/nk:budget --all` | every project in the store |

Over budget is a `warn` from `/nk:doctor`, not an error here. This command answers *how much am I
using*, not *what is broken*.
