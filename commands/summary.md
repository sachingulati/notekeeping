---
description: Write summary.md - what happened, in plain language, for someone who was not involved.
argument-hint: "[id] [--dry-run] [--page | --no-page] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git log:*), Bash(git diff:*), Bash(git -C:*), Artifact
---

Generate `summary.md` for a work item. Writes inside the store only.

**`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

**Resolve the store first**, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, then the item:
`[id]`, or the inference `/nk:save` step 1 makes, resolved against `index.md` - acting on a confident match and asking
where there is none, as `/nk:save` does.

This is the **only generated file inside a bundle**, and the only one safe to throw away and rebuild.

Resolve the `summary.md` definition per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` - the
user's overlay wins over the shipped default - and honour its admission and exclusion tests.

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

Plain human language throughout. The budget is the resolved definition's `budget:` - 3000 bytes as shipped. It is
meant to be pasted into a comment or a release note, and something too long to paste does not get pasted.

State where the source material came from - the bundle, the commits, the merge request - so the
summary can be rebuilt when the work moves on.

**`--dry-run`** prints what would be written and writes nothing.

## The summary as a page

**Print the summary after writing it**, then offer it as a page others can read and comment on, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`, *A summary as a page* - which owns the offer, the
two flags, the `Page:` line this command keeps in `summary.md`, and updating that page on a later run.
