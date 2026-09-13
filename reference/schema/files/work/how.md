---
file:        how.md
scope:       work
enabled:     false
tier:        example
shape:       register
owner:       nk:how
trigger:     the area was unfamiliar enough to be worth teaching
authority:   original
budget:      none
env_axis:    optional
---

## Question
How would I do this myself, without AI?

## Admission
What you had to understand about the terrain before you could act: how the pieces connect here, what
the mechanism actually is, which part is load-bearing.

## Exclusion
What was asked -> requirements.md. What you did -> dev.md. A construction approach the whole project
should inherit -> promote to patterns.md.

## Entry format
- **The thing you had to understand.** How it actually works, naming the identifier or path that
  shows it. `(source - date)`

This file ships **disabled**. It is the most valuable file in the set for an unfamiliar codebase and
the least universal, so it is a schema entry rather than a default - set `enabled: true` to turn it
on. It is also a worked example of a personal-practice file: copy this shape to add your own.
