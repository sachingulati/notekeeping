---
description: Write api.md - contracts while they are still moving. Disabled by default.
argument-hint: "[id] [--dry-run] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

Fill in `api.md` for a work item. Writes inside the store only.

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: never ask - refuse naming the argument
that would satisfy it - and end with the outcome line.

Resolve the `api.md` definition per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` - the
user's overlay wins over the shipped default - and honour its admission and exclusion tests.

**This file ships disabled.** If the resolved definition is not enabled, say so and point at the one
line of overlay that turns it on - do not write it anyway.

## What goes in

A contract this work introduces, changes, or depends on, **while it is still in flux**: the contract
named, its shape, what changed, and what depends on it.

Deviations are noted under the relevant entry rather than replacing it - the point is to see the
contract move.

## The pair

`api.md` declares `promotes_to: interfaces.md`. When the contract stops changing across two
consecutive saves, or the item closes, it promotes into the project's settled contracts. **You do not
implement that flow** - declaring the pair is what gives you routing, deduplication, the promotion
verdicts and contradiction handling.

Say when an entry looks ready to promote. Do not promote silently.
