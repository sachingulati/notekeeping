---
file:        log.md
scope:       work
schema:      4
enabled:     true
shape:       ledger
owner:       nk:adopt, nk:doctor, nk:review, nk:upgrade - the run engine
trigger:     a run item's first apply
authority:   original
budget:      none - a ledger is never trimmed
---

## Question
What did this run write, and did each write finish?

## Admission
Every write and every trim an apply makes, recorded before it starts and after it lands; every copy
taken into `copies/`; every item found stale at its check.

## Exclusion
What the run found -> `report.md`. Why an item was answered as it was -> its `answer` line there.

## Entry format
**Append-only, and never edited.**

```markdown
<!-- nk: schema 4 -->
- <date> · 3 · start · <the write: the file, and what changes>
- <date> · 3 · done
- <date> · 5 · copy · <repo>/CLAUDE.local.md -> copies/repo-web/CLAUDE.local.md
- <date> · 5 · start · trim <repo>/CLAUDE.local.md, lines 12-14
- <date> · 5 · done
- <date> · 7 · stale · <what moved>
```

**`start` is written before the write and `done` after it**, each in its own write. **An item with a
`start` and no `done` is one an apply stopped inside**: the next apply checks its target - the write
already there is recorded `done`, one not there is made - and never assumes either.

**The item's `state` in `report.md` moves to `applied` only after its `done`.**

## Migration
**3 -> 4 - nothing to convert.** A release-3 store has no run items, so no `log.md`. `report.md`'s
own step writes the log of a report it converts, from that report's `## Applied`.
