---
file:        session.md
scope:       work
schema:      1
enabled:     true
tier:        core
shape:       ledger
owner:       nk:save
trigger:     work actually starts
authority:   original
budget:      none - a ledger is never trimmed
env_axis:    optional
---

## Question
What happened, in order?

## Admission
Anything that happened, dated. **Current state does not live here** - that is `resume.md`, and two
homes for one answer is the drift this split exists to end.

## Exclusion
A fact that outlives this item -> promote it. A conclusion about where the work now stands ->
`resume.md`. What you tried and rejected belongs in **both**: the attempt here, dated, and the
standing *do not repeat* line there.

## Entry format
**Append-only, and never edited.** That is this file's whole value: it is the record every other
account of this work is checked against.

```markdown
## session <date>
- what happened, in order
```

Re-running save on the same day extends today's block rather than opening a second one.

**Every block names its kind first, and that is what makes this file greppable.** Two kinds share it
- the sessions and the closing blocks - so a heading that carried only a date told a reader nothing
and told a `grep` even less. **`grep -n '^## session ' session.md | tail -1` is the latest session
block**, which is the half a `--quick` load needs and the one thing a date-only heading could not
give it.

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

**Who writes it.** `/nk:save` when the user says the work is done, and `/nk:review --apply` for
finding 9. **Never a command acting on its own judgement**: staleness proposes, a person decides.

## Migration

**None.** This definition ships at release 1 like every other at birth. It replaces half of the old
`dev.md`, which no store was ever written with, so there is nothing to convert.
