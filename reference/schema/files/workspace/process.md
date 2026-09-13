---
file:        process.md
scope:       workspace
enabled:     true
tier:        extended
shape:       register
owner:       promotion
trigger:     always
authority:   original
budget:      60 entries
env_axis:    optional
---

## Question
How does work move here - review, release, escalation?

## Admission
A process fact that holds across the projects in this workspace and that you have to know to get
something shipped.

## Exclusion
How to run or deploy one system -> that project's runbook.md. Who to ask -> people.md. True of how you
work whatever the employer or the machine -> global.

## Entry format
- **The claim, stated as something you could be wrong about.** The mechanism, naming the identifier
  or path that proves it. What to do instead. `(source - date)`
  > **Corrected <date> (source):** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. Provenance on every entry, where the
source is a work-item id, `meeting <slug>`, `session`, or a source you defined. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.

**Extended, not core.** On a solo workspace with no team and no release process there is nothing to
put here, and shipping it enabled-but-disableable is honest where shipping it as core produces a stub.
Most process is **workspace-scoped rather than global**: an employer's review and release rules belong
to the workspace that holds that employer's projects, not to your working life in general.
