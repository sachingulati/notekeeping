---
doc: pending
title: Setting facts aside, and promoting them later
---

## Setting facts aside

**`/nk:save --no-promote` writes the item's record and promotes nothing.** Promotion and the drain
wait for the next save without it. Promotion reads the conversation, and `/clear` ends the
conversation, so **what promotion would weigh is written down now** - in the item's `pending.md`,
per its definition.

**Write each fact the session taught that outlives this item** - a decision, a pattern or a command
as much as a gotcha - as promotion would find its candidates, and leave the judgement to the save that promotes: no routing, no scope, no dedupe
against the registers, no register read. **The line is the whole fact** - what it is, where it
holds, and what showed it - because the conversation will not be there to fill it in.

- **The item it was learned on holds it.** With several targets, the one whose work taught it; where
  that is unclear, the first target in id order.
- **A fact the file already holds is not added twice** - this save reads the item's `pending.md`
  before it writes.
- **A credential is never set aside.** One the user asked to keep in this session is the one write
  promotion still makes under `--no-promote` - to `secrets.md`, per the promotion rule's
  *Credentials are never copied*. Any other is left out, as everywhere.
- **The harness memory is left where it is.** It survives `/clear` on its own, and the next save
  that promotes drains it.

**Nothing to set aside writes nothing** - no empty file is created.

## Promoting what was set aside

**A save that promotes takes in every item's `pending.md`, not only its targets'** - a fact set
aside on one item is promoted by the next save on any. Find them in one call: `Grep` `^- ` with
`glob` `pending.md`, `output_mode` `files_with_matches` and `path` `<store>/work`. An empty
`pending.md` does not match, and is nothing waiting. **No match means nothing is waiting this save
can see**; what a failed search missed stays where it is for the next one.

**Each line is a candidate, beside the session's own**, through the same promotion - routed, scoped,
deduped and written per the promotion rule. **Its source is the item that
holds it**, for scope as for the record. A line and a session candidate that say the same thing are
one candidate. **The line is the fact, not the entry**: the entry is written in its register's own
format - its numbering, its trailer, its date - as a session candidate's would be, and none of the
line's own wording about where it goes or where it came from is copied into it.

## Trimming

**A line leaves `pending.md` only once its fact is settled and the write that settled it has
landed.** The trims are written after the promotion writes have answered, never in the same message.

| The verdict | The line |
|---|---|
| written, merged, or already there | removed |
| not worth recording | removed |
| a one-off | removed, its text carried by the ledger line below |
| held back - a question unanswered, the target register behind its definition | stays, `held <date>: <why>` before the fact |

**Rewrite the file with what stays**; with nothing staying, write it empty.

## The ledger line

**Trimming loses nothing because the ledger records it.** For each item whose `pending.md` this save
promoted from, one line in the session block this save writes - that item's own block where it is a
target, otherwise the first target's, naming the item:

```markdown
- promoted from pending (<id>): <n> - <file>, <file>; one-off: <text>; held: <n>
```

## The report

**A `--no-promote` save reports what it set aside** above the line it ends on:

> Set aside 3 facts in `<id>/pending.md` - promotion and the memory drain wait for the next save
> without `--no-promote`.

**A save that promotes names what came from pending** with each verdict, and what stayed held.
