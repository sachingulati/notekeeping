---
file:        gotchas.md
scope:       global
enabled:     true
tier:        core
shape:       register
owner:       promotion
trigger:     always
authority:   original
budget:      60 entries
env_axis:    optional
---

## Question
What tool or environment trap follows me between codebases?

## Admission
You would not have predicted it, it cost time once, and it is not specific to one project.

## Exclusion
Specific to one project -> that project's gotchas.md. You simply did not know it -> the content type
it belongs to.

## Entry format
- **The claim, stated as something you could be wrong about.** The mechanism, naming the identifier
  or path that proves it. What to do instead. `(source - date)`
  > **Corrected <date> (source):** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. Provenance on every entry, where the
source is a work-item id, `meeting <slug>`, `session`, or a source you defined. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.
