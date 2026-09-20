---
file:        interfaces.md
scope:       project
schema:      1
enabled:     false
tier:        example
shape:       register
owner:       promotion
trigger:     always
authority:   derived where a generator exists, with the recipe in the header; original otherwise
budget:      60 entries
env_axis:    optional
paired_from: api.md
---

## Question
What contracts does this project expose or consume, that have settled?

## Admission
A contract that **stopped changing**, or whose work item closed.

## Exclusion
Still in flux -> the work item's api.md. The *meaning* of a field rather than its shape -> domain.md.

## Covers
HTTP APIs - a library's public surface - CLI flags and exit codes - message and event schemas -
database contracts - config contracts.

## Entry format
- **The claim, stated as something you could be wrong about.** The mechanism, naming the identifier
  or path that proves it. What to do instead. `(source - date)`
  > **Corrected <date> (source):** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. Provenance on every entry, where the
source is a work-item id, `meeting <slug>`, `session`, or a source you defined. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.

Where a generator exists, the body is generated and the header carries the recipe:

```markdown
<!-- authority: derived - owned by the contract document; regenerate:
     <the exact command> -->
```

A generated body plus a hand-written caveat - why reading the annotations gives the wrong answer - is
the model case for the whole `derived` idea.

This file ships **disabled** as the project half of the shipped pair; enable it with api.md.
