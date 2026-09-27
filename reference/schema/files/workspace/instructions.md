---
file:        instructions.md
scope:       workspace
schema:      1
enabled:     true
tier:        core
shape:       document+append
owner:       promotion
trigger:     always
authority:   original
budget:      2000 bytes
env_axis:    optional
---

## Question
What must I *do* everywhere under this root, whatever I am working on?

## Admission
You asked for this to be standing behaviour, and it holds across the projects in this workspace
rather than in one of them. **An instruction is obeyed, not looked up**, so it carries no correction
shape and lives outside the registers.

## Exclusion
True of one project -> that project's `instructions.md`. A fact that is true -> `NOTES.md`, or the
register it belongs to. True of every workspace on the machine -> the global `instructions.md`.

## Entry format
The same shape as a project's `instructions.md`, and **the same ceiling**.

**Every scope that admits an instruction gets the same 2000 bytes**, deliberately: a rule that holds
across a workspace is not a smaller rule than one that holds in a project, and three different
numbers made the author's job a budgeting exercise rather than a question about scope. The same
holds for the projection: this file is rendered into `<workspace-root>/CLAUDE.local.md`, charged in
**every** session under that root, including ones working on no project at all, and
`projection_bytes` is sized to hold it beside the facts rather than against them.

**Nearest scope wins where two levels disagree**, and `/nk:doctor` reports the pair rather than
resolving it: a project instruction is both more specific and loaded closer to the work, and a
silent override is the failure worth catching.
