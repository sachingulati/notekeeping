---
name: plan
description: Write the work item's plan.md - how the work will be done - from what the session has settled. Use when the user asks to record the agreed plan for a tracked piece of work.
argument-hint: "[id] [--dry-run]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

Fill in `plan.md` for a work item. Writes inside the store only.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

Then the item:
`[id]`, or the item resolved per `${CLAUDE_PLUGIN_ROOT}/reference/item.md` -
acting on one match and asking where there are several or none (`## Asking`).

Resolve the `plan.md` definition per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` - the
user's overlay wins over the shipped default - and honour its admission and exclusion tests.

## Writing the plan

**Record the plan; never make one.** The plan is what the conversation holds - settled there, written
to a file the session wrote or read, or stated by the call that started this skill. Write it as it
stands: its approach, its steps in their order, and whatever dependencies and alternatives it names.
Add nothing it does not say and ask for nothing it left out - a three-step plan is written as three
steps.

Always rewrite `plan.md`. The only question this skill asks is which item (`## Asking`).

**No plan in the conversation → write nothing**, and say there is none to record. Never draft one.

**`--dry-run`** prints what would be written and writes nothing.

## Asking

**Every question this skill asks follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`** - `nk:plan
needs:` and the open questions.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:plan`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.
