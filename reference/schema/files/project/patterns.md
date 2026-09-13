---
file:        patterns.md
scope:       project
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
How do we build X here?

## Admission
A repeatable construction approach **with a canonical implementation you can name** - a path or an
identifier.

## Exclusion
One-off -> dev.md. Operational rather than build-time -> runbook.md. A choice among alternatives ->
decisions.md. "The obvious way breaks" -> gotchas.md.

## Entry format
- **The claim, stated as something you could be wrong about.** The mechanism, naming the identifier
  or path that proves it. What to do instead. `(source - date)`
  > **Corrected <date> (source):** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. Provenance on every entry, where the
source is a work-item id, `meeting <slug>`, `session`, or a source you defined. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.

**The named canonical implementation is required.** Without one it is not a pattern, it is an
opinion, and it is not admitted.
