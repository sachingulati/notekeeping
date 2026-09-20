---
file:        areas/
scope:       global
schema:      1
enabled:     true
tier:        extended
shape:       container
owner:       nk:review creates it; promotion appends to it
trigger:     a register passes its split threshold and one topic dominates it
authority:   original
budget:      none - each file inside keeps the budget of the register it came from
env_axis:    optional
---

## Question
Which topic in my working life has earned a shelf of its own?

## Admission
A topic that **dominates** one of global's registers past its split threshold, and that is about your
work rather than about any codebase.

## Exclusion
About one codebase -> it does not belong at global scope at all. True of the projects in one
workspace -> demote it there, then split at that scope. A long register with no dominant topic -> it
stays one register.

## Entry format
**The same shape as a project's area**, stated once at
`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/project/areas.md`. Here an area sits at
`~/.notekeeping/areas/<topic>/`, and the registers that can split are `gotchas.md`, `decisions.md`,
`domain.md`, `people.md` and `process.md`.

**Global has no projection**, so an area here is reached by reading rather than by delivery, and
nothing needs to be rebuilt before it is reachable.

**Extended, not core.** Global is the tightest scope in the store: a register that reaches a split
threshold here is unusual, and is worth a second look at whether its entries were really global.
