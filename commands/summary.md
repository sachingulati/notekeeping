---
description: Write summary.md - what happened, in plain language, for someone who was not involved.
argument-hint: "[id] [--dry-run] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git log:*), Bash(git diff:*)
---

Generate `summary.md` for a work item. Writes inside the store only.

**`--caller <name>`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

This is the **only generated file inside a bundle**, and the only one safe to throw away and rebuild.

Resolve the `summary.md` definition per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` - the
user's overlay wins over the shipped default - and honour its admission and exclusion tests.

## When the trigger has not fired

The trigger - the item closed, or its change merged - governs when `/nk:save` **offers** a summary.
It does not gate an explicit invocation. If someone types this command on an open item, **write the
summary and say the trigger has not fired**, so what they are holding is not mistaken for a closing
record. This file is regenerable, so an early one costs nothing.

## Audience

You after a long gap, a reviewer, a product owner, QA, an auditor - in that order of frequency.
Assume none of them remembers the work, **including when that reader is you.**

## Four sections

```markdown
## What was wrong
## What changed
## How to check it
## What is still open
```

## The constraint that does the work

**No file paths, no class names, no diffs.** One pointer at most. A path means nothing to a reviewer
and nothing to you in eight months - that is why the rule is absolute rather than a preference.

Plain human language throughout. Budget is 3000 bytes: it is meant to be pasted into a comment or a
release note, and something too long to paste does not get pasted.

State where the source material came from - the bundle, the commits, the merge request - so the
summary can be rebuilt when the work moves on.

**`--dry-run`** prints what would be written and writes nothing.
