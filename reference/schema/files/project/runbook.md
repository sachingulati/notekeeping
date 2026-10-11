---
file:        runbook.md
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
How do I run, deploy, inspect or recover this?

## Admission
Copy-pasteable, operational, and **verified in a named environment**.

## Exclusion
Build-time rather than run-time -> patterns.md. Not yet verified anywhere -> not admitted yet.

## Entry format
- **The task, stated as something you could be wrong about.** The command or steps, copy-pasteable,
  and the environment they were verified in.
  > **Corrected <date>:** what the earlier version claimed, and what it cost.

Bold claim first - that is what keeps a long register scannable. No entry carries its source or a date: the claim is
what is read, and where it came from is the work item or report that wrote it. Corrections are
inline and never overwrite: the wrong answer is what a future session would otherwise re-derive.
About 120 words per entry; longer means the narrative belongs in a work bundle, and the entry cites
it. No manual numbering.

**The environment axis on the verified stamp is mandatory here**, as it is on `test.md`, and
optional everywhere else.
Operational commands break because the cluster moved, not because the code did, so a runbook entry
that does not name where it was verified is not verified.
