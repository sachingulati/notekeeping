---
file:        architecture.md
scope:       project
schema:      1
enabled:     true
shape:       register
owner:       promotion
trigger:     always
authority:   original
budget:      60 entries
---

## Question
How is this put together, and why is responsibility split the way it is?

## Admission
A structural fact that **changes how you would approach a task**, and is not derivable from the
directory layout.

## Exclusion
"Where the code lives" -> overview.md's diagram. "Why we chose this structure" -> decisions.md. "This
structure will bite you" -> gotchas.md.

## Entry format
- **The claim, stated as something you could be wrong about.** The mechanism, naming the identifier
  or path that proves it. What to do instead.
  > **Corrected <date>:** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. No entry carries its source or a date: the claim is
what is read, and where it came from is the work item or report that wrote it. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.
