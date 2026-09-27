---
file:        resume.md
scope:       work
schema:      1
enabled:     true
tier:        core
shape:       document
owner:       nk:save
trigger:     work actually starts
authority:   original
budget:      notice at 30000 bytes - read on demand, never capped
env_axis:    optional
---

## Question
Where does this stand, and what do I need to know before touching it again?

## Admission
**What a cold session must be told to carry the work forward knowingly**: the position, how it got
here, why it was done this way, and what has already been ruled out.

## Exclusion
Raw narrative of what happened -> `session.md`, which is the record this file is derived from. A
fact that outlives this item -> promote it. **What the code is** -> the code; a summary that
restated the repository would be a second, worse copy of it.

## Entry format

**One rewritten block, then four that accumulate.** The position at the top is regenerated at every
save because it is only ever about now. Everything below it is the story, and it grows.

```markdown
## Where things stand
<!-- rewritten every /nk:save - the first thing /nk:load reads -->
**Branch**    <branch> - <n> ahead of origin - <n> dirty files
**Done**      what is finished and verified
**In flight** what is started, and where it is
**Next**      the next decision or action
**Blocked**   what is stuck, and on whom
**Verify**    the command that proves it works

## Covers
sessions <first>-<last>, through <date> - re-derived | carried forward

## The story so far
<what this set out to do, what exists now, and how it got here>

## Decisions and why
### <date> - session <n> - <what was decided>
**Why**       <the reason, which git does not carry>
**Touched**   <files>

## Tried and rejected - do not repeat
1. <approach> - <why it failed> (session <n>)
<n> rejected

## What is left
<what remains, in the order it should be taken>
```

**The dirty count is the one `ignore_dirty` bounds** - the configured local-only paths are excluded,
and an unset key excludes nothing (`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`). It is
written by `/nk:save` and read back by `/nk:load`, so both count it the same way.

## Three rules, and each one closes a way this file rots

**1 · `Covers` names its range and how it was built.** `re-derived` means this save rebuilt the
story from `session.md`, which it had in context; `carried forward` means it did not, and the
previous text was extended instead. A file that says *carried forward* **five saves running** is a file
drifting from the record - that is the threshold, and `/nk:doctor` warns at it - and `/nk:doctor` compares this line against `session.md`'s last session
block rather than trusting it.

**2 · The rejected list may be reworded, never shortened, while the item is open.** A dead end is
the most expensive thing in here and the easiest to lose in a rewrite. It is numbered, and **the
count is the last number in the list** - read off it, never formed separately
(`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`), so a list that lost an entry says so.

**3 · Nothing here is trusted over `session.md`.** This file is derived; that one is the record.
Where they disagree the record wins, and the disagreement is worth reporting rather than quietly
resolving.

## Why this is not capped

**It is read on demand, once per load - never always-loaded**, so the ceilings in
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md` do not apply and neither does their reasoning.
Capping a file whose job is to let the work be rebuilt knowingly would defeat the file. The notice
threshold, 30000 bytes, is well above every document budget that ships, and is **a notice, not a
limit**: past it, the likely cause is narrative that belongs in `session.md`, and
that is what the notice says.

## Migration

**None.** This definition ships at release 1 like every other at birth. It replaces half of the old
`dev.md`, which no store was ever written with, so there is nothing to convert.
