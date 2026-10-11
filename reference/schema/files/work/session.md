---
file:        session.md
scope:       work
schema:      1
enabled:     true
shape:       ledger
owner:       nk:save
trigger:     work actually starts
authority:   original
budget:      none - a ledger is never trimmed
---

## Question
What happened, in order?

## Admission
Anything that happened, dated. **Current state does not live here** - that is `resume.md`, once the
item has one. Before then the one exception is the `next:` line below.

## Exclusion
A fact that outlives this item -> promote it. A conclusion about where the work now stands ->
`resume.md`. What you tried and rejected belongs in **both**: the attempt here, dated, and the
standing *do not repeat* line there.

## Entry format
**Append-only, and never edited.** That is this file's whole value: it is the record every other
account of this work is checked against.

```markdown
## session <date>
session: <session id>
- what happened, in order
```

**One `session:` line per session the block covers**, added once when that session first writes the
block. Save counts them to know when `resume.md` starts (`work/resume.md`, *When it starts*).

**Before the item has `resume.md`, a save ends the block it writes with one line** -
`next: <the next step>` - rewritten in place when the same day's block is extended. It is where the
work stopped, as of that block: the latest block's line is the position, and an earlier one is
history. Once `resume.md` exists, blocks carry none.

Re-running save on the same day extends today's block rather than opening a second one.

**Every block names its kind first, and that is what makes this file greppable.** Three kinds share it
- `session`, `closed` and `open`. **The last `^## session ` match, from `Grep` with line
numbers, is the latest session block.**

## Closing, and staying open

**A work item is closed by a dated block in this file, and its shape is fixed here** so that every
writer produces the same thing and a reader can find it without parsing prose:

```markdown
## closed <date>
<one line: why it is closed - shipped, merged, abandoned, superseded by <id>>
```

**The same block with the other verb says it is deliberately open**, which is what keeps an old item
from being reported as stale for the rest of its life:

```markdown
## open <date>
<one line: why it is still open - waiting on <what>, parked until <when>>
```

**One heading, one line, and nothing else.** The reason is the whole content: a closing block that
narrates belongs in the session block above it. **The date is the day it was written**, and a later
block supersedes an earlier one, so reopening is a new `## open <date>` rather than an edit.

**Neither block is a status field**, and the resolver gains no column for it - a work item still
holds no workflow state (`requirements.md`, *no status field*). This is a dated entry in an
append-only region, exactly like every other block here, and it is read by looking at the last one.

**Who writes it.** `/nk:save` when the user says the work is done, and `/nk:review --apply`, when it
applies a close it proposed. **Never a command acting on its own judgement**: staleness proposes, a
person decides.
