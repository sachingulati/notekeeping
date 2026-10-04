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
**Branch**    <branch>, or `detached`
**Done**      what is finished and verified
**In flight** what is started, and where it is
**Next**      the next decision or action
**Blocked**   what is stuck, and on whom
**Verify**    the command that proves it works

## Covers
`## session` blocks <first>-<last>, through <date> - re-derived | carried forward

## The story so far
<what this set out to do, what exists now, and how it got here>

## Decisions and why
### <date> - session block <n> - <what was decided>
**Why**       <the reason, which git does not carry>
**Touched**   <files>

## Tried and rejected - do not repeat
1. <approach> - <why it failed> (session block <n>)
<n> rejected

## What is left
<what remains, in the order it should be taken>
```

**The branch is read per the repo-facts rule**, `unknown` when it could not be. No dirty count and
no commits-ahead count are recorded. A position block that carries either is left
as it is - the next save rewrites the position.

## Rules

**1 · `Covers` names its range and how it was built.** `re-derived` means this save rebuilt the
story from `session.md`, which it had in context; `carried forward` means it did not, and the
previous text was extended instead. A file that says *carried forward* **five saves running** is a file
drifting from the record - that is the threshold, and `/nk:doctor` warns at it - and `/nk:doctor` compares this line against `session.md`'s last session
block rather than trusting it.

**2 · The rejected list may be reworded, never shortened, while the item is open.** A dead end is
the most expensive thing in here and the easiest to lose in a rewrite. It is numbered, and **the
count is the last number in the list** - read off it, never formed separately
(the report shape), so a list that lost an entry says so.

**3 · Nothing here is trusted over `session.md`.** This file is derived; that one is the record.
Where they disagree the record wins, and the disagreement is worth reporting rather than quietly
resolving.

## Budget

**It is read on demand, once per load - never always-loaded**, so no always-loaded ceiling
applies; the notice threshold is **a notice, not a limit** - past it, the likely cause is narrative
that belongs in `session.md`.
