---
file:        gotchas.md
scope:       project
schema:      1
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
What here contradicts a reasonable expectation?

## Admission
**You would not have predicted it**, it cost time once, and it would cost time again.

## Exclusion
**You simply did not know it** -> the content type it belongs to: domain - patterns - architecture -
runbook. One-off, this item only -> session.md.

## Entry format
- **The claim, stated as something you could be wrong about.** The mechanism, naming the identifier
  or path that proves it. What to do instead. `(source - date)`
  > **Corrected <date> (source):** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. Provenance on every entry, where the
source is a work-item id, `meeting <slug>`, `session`, or a source you defined. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.

The exclusion test is this file's whole boundary. A hazard is a fact whose value is that it
contradicts a reasonable expectation: if you would not have predicted it, it belongs here; if you
simply did not know it, it belongs to its content type. Without that test everything qualifies, and
this file becomes the place knowledge goes to be lost.
