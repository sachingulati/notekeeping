---
file:         api.md
scope:        work
enabled:      false
tier:        example
shape:        register
owner:        nk:api
trigger:      a contract appears - new, changed, or consumed
authority:    original
budget:       none
env_axis:     optional
promotes_to:  interfaces.md
promote_when: the contract stops changing across two consecutive saves, or the item closes
---

## Question
What are the contracts, while they are still moving?

## Admission
A contract this work introduces, changes, or depends on, while it is still in flux.

## Exclusion
A contract that has settled -> it promotes to interfaces.md. What a field *means* rather than what
shape it has -> the project's domain file.

## Entry format
- **The contract**, named. Its shape. What changed, and what depends on it. `(source - date)`

Deviations are noted under the relevant entry rather than replacing it.

This file ships **disabled**, and it is the demonstration of the **pair pattern**: declaring
`promotes_to:` gives you routing, deduplication, the promotion verdicts and contradiction handling
without implementing any of it. Copy this shape to add your own pair.
