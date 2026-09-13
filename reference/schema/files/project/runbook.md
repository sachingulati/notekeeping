---
file:        runbook.md
scope:       project
enabled:     true
tier:        extended
shape:       register
owner:       promotion
trigger:     always
authority:   original
budget:      60 entries
env_axis:    required
---

## Question
How do I run, deploy, inspect or recover this?

## Admission
Copy-pasteable, operational, and **verified in a named environment**.

## Exclusion
Build-time rather than run-time -> patterns.md. Not yet verified anywhere -> not admitted yet.

## Entry format
- **The claim, stated as something you could be wrong about.** The mechanism, naming the identifier
  or path that proves it. What to do instead. `(source - date)`
  > **Corrected <date> (source):** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. Provenance on every entry, where the
source is a work-item id, `meeting <slug>`, `session`, or a source you defined. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.

**The environment axis on the verified stamp is mandatory here** and optional on every other file.
Operational commands break because the cluster moved, not because the code did, so a runbook entry
that does not name where it was verified is not verified.
