---
file:        areas/
scope:       global
schema:      1
enabled:     true
tier:        extended
shape:       container
owner:       nk:review creates it; promotion appends to it
trigger:     three or more entries on one topic across this scope's registers, or a register past its split threshold with one topic dominating it
authority:   original
budget:      none - each file inside keeps the budget of the register it came from
---

## Question
Which topic in my working life has earned a shelf of its own?

## Admission
**Three or more entries on one topic** across global's registers - or a topic that **dominates** one
of them past its split threshold - about your work rather than about any codebase. **A topic is judged by what the entries say**, never by a shared tag: `q3` or `urgent` on thirty items is a label, not a topic.

## Exclusion
About one codebase -> it does not belong at global scope at all. True of the projects in one
workspace -> demote it there, then split at that scope. A long register with no dominant topic -> it
stays one register. Fewer than three entries -> not yet an area.

## Entry format
**The same shape as a project's area**, stated once in
the project area definition. Here an area sits at
`~/.notekeeping/areas/<topic>/`, and the registers that can split are `gotchas.md`, `domain.md` and
`process.md`. `environment.md` does not split: it is bounded in bytes, not entries, because it is
always loaded.

**An area here is reachable through its row in `~/.notekeeping/areas/INDEX.md`**, as a project's
is; a split writes both in the same pass.

**Extended, not core.** Global is the tightest scope in the store: a register that reaches a split
threshold here is unusual, and is worth a second look at whether its entries were really global.
