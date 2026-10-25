---
file:        resume.md
scope:       work
schema:      1
enabled:     true
shape:       document
owner:       nk:save
trigger:     the item's fifth saving session
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

## When it starts

**At the item's fifth saving session, and not before.** Until then `session.md` is short enough to
read whole, and it is the record: a summary of four sessions would restate them. The count is the
sessions the record names - the distinct ids on its `session:` lines, `Grep` `^session: ` and
counted by value, so a session that saved on two days counts once; or its `## session` blocks where
those are more. Derived at each save, never
stored. The save that reaches five writes the first one, re-derived,
and every save after rewrites it. Until it exists, the position is the latest session block's
`next:` line (`work/session.md`). **An item that already has one keeps it**, whatever its count.

**A run item writes it at the end of every run** (the run-engine rule), and it holds
`## Where things stand` alone, rewritten each time:

```markdown
## Where things stand
**Items**    14 - 6 applied, 3 declined, 2 open, 3 carried
**Next**     the next action - answer 7, apply the open items, or nothing
**Left**     what the run left for the next one - adopt's pending set, each file with its size and what pointed at it | nothing
```

The counts are read off `report.md`'s states, as arithmetic per the report shape. A run has no story
to carry: `report.md` and `log.md` are its record.

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
`## session` blocks <first>-<last>, through <date> - re-derived in <session id> | carried forward since <session id>

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

**The branch is read per the repo-facts rule**, `unknown` when it could not be. The branch alone is
recorded - no dirty count, no commits ahead.

## Rules

**1 · `Covers` names its range and how it was built.** `re-derived in <id>` means this save, in
session `<id>`, rebuilt the story from `session.md`, which it had in context. `carried forward since
<id>` means it did not, and the previous text was extended instead; `<id>` is the session that last
re-derived it, copied from the previous `Covers`. **Five sessions carried forward is the threshold**:
the distinct ids, other than `<id>`, on `session.md`'s `session:` lines below `<id>`'s first one -
`Grep` `^session: ` with line numbers, the count *When it starts* uses. At five, `/nk:doctor` warns.
`/nk:doctor` also compares this line against `session.md`'s last session block rather than trusting
it.

**2 · The rejected list may be reworded, never shortened, while the item is open.** A dead end is
the most expensive thing in here and the easiest to lose in a rewrite. It is numbered, and **the
count is the last number in the list** - read off it, never formed separately
(the report shape), so a list that lost an entry says so.

**3 · Nothing here is trusted over `session.md`.** This file is derived; that one is the record.
Where they disagree the record wins, and the disagreement is worth reporting rather than quietly
resolving.

## Budget

**It is read on demand, once per load - never always-loaded**, so no always-loaded budget
applies; the notice threshold is **a notice, not a limit** - past it, the likely cause is narrative
that belongs in `session.md`.
