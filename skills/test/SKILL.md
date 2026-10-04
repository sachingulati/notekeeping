---
name: test
description: Write the work item's test.md and test-manual.md - how it is verified, by machine and by hand. Use when the user asks to record how a tracked piece of work is tested.
argument-hint: "[id] [--dry-run]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

Fill in the verification pair for a work item. Writes inside the store only.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

Then the item:
`[id]`, or the item resolved per `${CLAUDE_PLUGIN_ROOT}/reference/item.md` -
acting on one match and asking where there are several or none (`## Asking`).

Both files are written, every time. They are a pair by policy: `test.md` is the reusable
protocol, `test-manual.md` is the human walkthrough. Someone running the check by hand and something
re-running it later need different documents, and writing only one leaves the other job undone.

They are written differently. `test.md` is a register: add to it, and never rewrite an entry -
a re-run records its new outcome under the check it re-ran. `test-manual.md` is a document, and is
rewritten to the walkthrough as it stands now.

Resolve both definitions - `test.md` and `test-manual.md` - per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, and honour their admission and exclusion
tests. The user's overlay wins over the shipped default.

**`--dry-run`** prints what would be written and writes nothing.

## Asking

**Every question this skill asks follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`** - `nk:test
needs:` and the open questions.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:test`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.
