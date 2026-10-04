---
file:        areas/
scope:       workspace
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
Which topic across these projects has earned a shelf of its own?

## Admission
**Three or more entries on one topic** across this store's own registers - or a topic that
**dominates** one of them past its split threshold - about the workspace rather than about any one
project in it. **A topic is judged by what the entries say**, never by a shared tag: `q3` or `urgent` on thirty items is a label, not a topic.

## Exclusion
About one project -> that project's area instead. Fewer than three entries -> not yet an area. True whatever workspace
you are in -> promote to global, then split there. A long register with no dominant topic -> it stays
one register.

## Entry format
**The same shape as a project's area**, and the rule is stated once in
the project area definition - the directory, the pointer the
split leaves, the no-nesting rule, the slug reused from an existing tag, and the row in
`areas/INDEX.md` that makes an area reachable. This scope changes only which registers can split: the store's own
`gotchas.md`, `domain.md` and `process.md`, which sit directly in `.notekeeping/`, so an area sits
at `.notekeeping/areas/<topic>/`.

**Extended, not core.** A workspace register reaches a split threshold far later than a project's,
because what is admitted here has already had to hold across more than one project.
