---
description: Write test.md and test-manual.md - how this is verified, by machine and by hand.
argument-hint: "[id] [--dry-run] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git diff:*), Bash(git log:*)
---

Fill in the verification pair for a work item. Writes inside the store only.

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: never ask - refuse naming the argument
that would satisfy it - and end with the outcome line.

**Both files are written, every time.** They are a pair by policy: `test.md` is the reusable
protocol, `test-manual.md` is the human walkthrough. Someone running the check by hand and something
re-running it later need different documents, and writing only one leaves the other job undone.

Resolve both definitions - `test.md` and `test-manual.md` - per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, and honour their admission and exclusion
tests. The user's overlay wins over the shipped default.

## `test.md` - the reusable protocol

What the change was, what to exercise, what the correct behaviour is, which surfaces are in scope,
what the regression risk is, and anything that proves the result objectively rather than by eye.

Written so a future session, or a different person, can re-run the verification cold.

**Record the outcome of the run that was actually performed, with its date and the environment it ran
in.** The environment axis is required here: a check passes or fails against a named environment, not
in the abstract. If nothing has been run yet, say so - do not imply a pass.

## `test-manual.md` - the human walkthrough

Ordered, unambiguous steps: the exact starting point, any setup or flag needed first, the precise
path to each surface, the expected result at each step, and what must look unchanged elsewhere.

**Call out explicitly anything only a human can do** - operating-system settings, browser rendering
flags, file pickers, real assistive technology.

**No repository detail.** It should read as instructions, not as a summary of the change.
