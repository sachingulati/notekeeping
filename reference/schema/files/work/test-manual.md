---
file:        test-manual.md
scope:       work
schema:      1
enabled:     true
tier:        extended
shape:       document
owner:       nk:test
trigger:     the same trigger as test.md - a pair by policy
authority:   original
budget:      none
env_axis:    required
pairs_with:  test.md
---

## Question
How does a human check this by hand?

## Admission
Ordered, unambiguous steps: where to go, what to do, what should happen at each step, and what must
look unchanged elsewhere. Anything only a person can do is called out explicitly.

## Exclusion
Repository detail of any kind. This file should read as instructions, not as a summary of the change.
Automated or scripted checks -> test.md.

## Entry format
A numbered walkthrough. State the starting point, any setup or flag needed first, then each step with
its expected result. End with what must be unaffected.

Written for someone who was not involved and cannot read the code.
