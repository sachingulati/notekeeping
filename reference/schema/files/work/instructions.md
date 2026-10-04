---
file:        instructions.md
scope:       work
schema:      1
enabled:     true
tier:        extended
shape:       document+append
owner:       nk:save
trigger:     the item has standing behaviour of its own
authority:   original
budget:      2000 bytes
---

## Question
What must I *do* while this item is in flight, that is not true once it lands?

## Admission
The user asked for this to be standing behaviour **for this item** - a constraint the work carries
rather than one the project does. *While on this, run the contract tests before pushing.*

## Exclusion
Outlives the item -> the project's `instructions.md`, promoted like anything else. A fact about what
is being built -> `requirements.md`. A decision taken before execution -> `plan.md`. **A step
somebody performs by hand** -> `test-manual.md`, which reads as instructions for a different reason:
those are performed once and reported, these are obeyed throughout.

## Entry format
The same shape as a project's `instructions.md`.

## Lifetime

**This file is not projected.** It is read by `/nk:load` when the item is resumed, which is
deterministic for exactly the span where it applies - and it stops being read when the item closes.
An instruction that should keep firing after the item lands was
never a work-scope instruction; promote it.

`/nk:load` reads this file whole, once per resume.
