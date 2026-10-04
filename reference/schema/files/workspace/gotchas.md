---
file:        gotchas.md
scope:       workspace
schema:      1
enabled:     true
tier:        core
shape:       register
owner:       promotion
trigger:     always
authority:   original
budget:      60 entries
---

## Question
What trap bites in more than one project here?

## Admission
You would not have predicted it, it cost time once, and it holds across this workspace rather than in
one project.

## Exclusion
Specific to one project -> that project's gotchas.md. Follows you between workspaces too -> global.
You simply did not know it -> the content type it belongs to.

## Entry format
- **The claim, stated as something you could be wrong about.** The mechanism, naming the identifier
  or path that proves it. What to do instead. `(source - date)`
  > **Corrected <date> (source):** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. Provenance on every entry, where the
source is a work-item id, `meeting <slug>`, `session`, or a source you defined. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.
