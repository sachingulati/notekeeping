---
file:        areas/
scope:       workspace
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
Which topic across these projects has earned a shelf of its own?

## Admission
A topic that **dominates** one of this store's own registers past its split threshold, and that is
about the workspace rather than about any one project in it.

## Exclusion
Dominant in one project's register -> split that project's register instead. True whatever workspace
you are in -> promote to global, then split there. A long register with no dominant topic -> it stays
one register.

## Entry format
**The same shape as a project's area**, and the rule is stated once at
`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/project/areas.md` - the directory, the pointer the
split leaves, the no-nesting rule, the slug reused from an existing tag, and the rebuild that makes
an area reachable. This scope changes only which registers can split: the store's own
`gotchas.md`, `decisions.md`, `domain.md` and `process.md`, which sit directly in
`.notekeeping/`, so an area sits at `.notekeeping/areas/<topic>/`.

**Extended, not core.** A workspace register reaches a split threshold far later than a project's,
because what is admitted here has already had to hold across more than one project.
