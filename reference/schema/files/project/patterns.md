---
file:        patterns.md
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
How do we build X here?

## Admission
A repeatable construction approach **with a canonical implementation you can name** - a path or an
identifier.

## Exclusion
One-off -> session.md. Operational rather than build-time -> runbook.md. A choice among alternatives ->
decisions.md. "The obvious way breaks" -> gotchas.md.

## Entry format
- **The claim, stated as something you could be wrong about.** The canonical implementation, by path
  or identifier, and how to follow it.
  > **Corrected <date>:** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. No entry carries its source or a date: the claim is
what is read, and where it came from is the work item or report that wrote it. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.

**The named canonical implementation is required.** Without one it is not a pattern, it is an
opinion, and it is not admitted.
