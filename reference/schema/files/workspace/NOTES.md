---
file:        NOTES.md
scope:       workspace
enabled:     true
tier:        core
shape:       document
owner:       nk:save
trigger:     always
authority:   original
budget:      4000 bytes
env_axis:    optional
---

## Question
What do I need in every session in this workspace, whatever project I am in?

## Admission
A fact true across the projects that sit in this workspace, needed often enough to be worth standing
context. Plus a pointer to what is read on demand.

## Exclusion
True of one project -> that project's NOTES.md. True whatever you are working on, in any workspace ->
global. Current work state -> the handover block in the active work item's dev.md, pointed at and
never copied.

## Entry format
The same shape as a project's NOTES.md, with a tighter ceiling: its projection -
`<workspace-root>/CLAUDE.local.md`, written whenever `projections.workspace` is on (the default) and
this file has content, whatever the project count (3.18) - is charged in
**every** session under the workspace root, including ones working on no project at all.

**Over budget, degrade - never truncate.** A file past its ceiling is delivered as a pointer and its
size, not as a silently shortened version. A budget that fails visibly is the difference between a
small file and a wrong one.
