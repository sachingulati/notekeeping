---
name: summary
description: Write the work item's summary.md - what happened, in plain language, for someone not involved - and offer it as a page. Use when the user asks to write up a tracked piece of work for others.
argument-hint: "[id] [--dry-run] [--page | --no-page]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact
---

Generate `summary.md` for a work item. Writes inside the store only.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

Then the item:
`[id]`, or the item resolved per `${CLAUDE_PLUGIN_ROOT}/reference/item.md` -
acting on one match and asking where there are several or none (`## Asking`).

This file is safe to throw away and rebuild - run `/nk:summary` again to regenerate it.

Resolve the `summary.md` definition per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` - the
user's overlay wins over the shipped default - and honour its admission and exclusion tests.

## Audience

The author after a long gap, a reviewer, a product owner, QA, an auditor - in that order of
frequency. Assume none of them remembers the work, including the author.

## Four sections

```markdown
## What was wrong
## What changed
## How to check it
## What is still open
```

Plain human language throughout, within the resolved definition's `budget:` - 3000 bytes as shipped.
It is meant to be pasted into a comment or a release note, and something too long to paste does not
get pasted.
A write at or past `budget_notice_pct` of that budget says so, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`.

State where the source material came from - the bundle, the conversation, and the merge request
where the session reaches the tracker, and **the commits since the item's first session** in each
of its projects' repositories, per `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md` - so the
summary can be rebuilt when the work moves on. Git unavailable: the commits are not a source, said.

**`--dry-run`** prints what would be written and writes nothing.

## The summary as a page

Print the summary after writing it, then ask *Publish / Not now* - a page others can read and
comment on - per
`${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`, *A summary as a page* - which owns the offer, the
two flags, the `Page:` line this command keeps in `summary.md`, and updating that page on a later run.

## Asking

**Every question this skill asks follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`** - `nk:summary
needs:` and the open questions.

**A re-entry after *Publish / Not now* finds the summary already written** in this conversation: it
publishes or leaves it as the answer says, and writes `summary.md` only to add the `Page:` line.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:summary`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.
