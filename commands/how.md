---
description: Write how.md - what you had to understand about the terrain. Disabled by default.
argument-hint: "[id] [--dry-run] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

Fill in `how.md` for a work item. Writes inside the store only.

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: never ask - refuse naming the argument
that would satisfy it - and end with the outcome line.

Resolve the `how.md` definition per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` - the
user's overlay wins over the shipped default - and honour its admission and exclusion tests.

**This file ships disabled.** If the resolved definition is not enabled, say so and point at the one
line of overlay that turns it on - do not write the file anyway.

## What goes in

What you had to understand about the terrain before you could act: how the pieces connect here, what
the mechanism actually is, which part is load-bearing, and what you would tell someone doing this
without any assistance.

Bold claim first, then the mechanism, naming the identifier or path that shows it.

## What does not

What was asked -> `requirements.md`. What you did -> `dev.md`. A construction approach the whole
project should inherit -> offer to promote it to `patterns.md` rather than leaving it here.

Write nothing rather than something thin. The value of this file is that it teaches; a paragraph that
restates the ticket teaches nothing.
