---
description: Show what the notes are actually spending - always-loaded, on-demand, and registers.
argument-hint: "[project] [--all] [--caller <name>]"
allowed-tools: Read, Glob
---

Report what is actually being spent. **Reads only - this command writes nothing.**

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`. This command only ever reads, so it behaves
normally - compress the figures into the outcome line's detail and emit nothing else.

Resolve the store first, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and measure only
what is inside it. The projections and their ceilings are defined in
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - read which targets exist and which flags govern
them there rather than assuming either.

Budgets are configurable, but a ceiling you cannot see yourself approaching is one you only meet as a
warning after the fact.

## Three sections, and the split is the point

**Always loaded** - charged on every prompt. Both projections - `<repo>/CLAUDE.local.md` and
`<workspace-root>/CLAUDE.local.md` - each as
*current / ceiling / percent* with a bar. Total in bytes, tokens, and as a share of
`context_window_tokens`. **A projection whose flag is off is reported as off, not as zero**: a
ceiling nobody is spending against is a different fact from one being spent to nothing.

**For scale, in the same session** - the harness's own always-loaded memory index, and the
repository's committed instructions file. Name the numbers beside ours. The point is honesty about
what we cost relative to what is already there.

**Source documents** - read on demand, not charged unless read. `NOTES.md` per project, the
workspace's own `NOTES.md`, `~/.notekeeping/NOTES.md`, `overview.md`.

**Registers** - no cap; the thresholds are proposals. Show size and entry count, and name the split
threshold rather than implying a limit.

## Always name the window

A share of context is only meaningful against a window: the same store is about 1.6% of 200K and
0.32% of 1M. **Report the value of `context_window_tokens` that was used.**

## Forms

| Form | Does |
|---|---|
| `/nk:budget` | every budgeted file for the current project, plus global |
| `/nk:budget <project>` | one project |
| `/nk:budget --all` | every project in the store |

Over budget is a `warn` from `/nk:doctor`, not an error here. This command answers *how much am I
using*, not *what is broken*.
