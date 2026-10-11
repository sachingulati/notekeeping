---
file:        process.md
scope:       global
schema:      1
enabled:     true
shape:       register
owner:       promotion
trigger:     always
authority:   original
budget:      60 entries
---

## Question
How does work move here - review, release, escalation?

## Admission
A process fact that holds across projects and that you have to know to get something shipped.

## Exclusion
How to run or deploy one system -> that project's runbook.md.

## Entry format
- **The claim, stated as something you could be wrong about.** The mechanism, naming the identifier
  or path that proves it. What to do instead.
  > **Corrected <date>:** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. No entry carries its source or a date: the claim is
what is read, and where it came from is the work item or report that wrote it. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.

Most process is **workspace-scoped rather than global** - an employer's
process belongs to the workspace holding that employer's projects. See
the workspace process definition.
