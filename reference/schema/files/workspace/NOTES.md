---
file:        NOTES.md
scope:       workspace
schema:      1
enabled:     true
tier:        core
shape:       document
owner:       nk:save
trigger:     always
authority:   original
budget:      12000 bytes
env_axis:    optional
---

## Question
What do I need in every session in this workspace, whatever project I am in?

## Admission
A fact true across the projects that sit in this workspace, needed often enough to be worth standing
context. Plus a pointer to what is read on demand.

## Exclusion
True of one project -> that project's NOTES.md. True whatever you are working on, in any workspace ->
global. Current work state -> the handover block in the active work item's resume.md, pointed at and
never copied. **Something to do rather than something that is true** -> `instructions.md` at this
scope, which is projected beside this file and is not pointed at from here.

## Entry format
The same shape as a project's NOTES.md, with a tighter ceiling: its projection -
`<workspace-root>/CLAUDE.local.md`, written whenever this file has content, whatever the project
count - is charged in
**every** session under the workspace root, including ones working on no project at all.

**Over budget, this file degrades rather than truncating** - stated once in
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, *Budgets, and what to drop*, and not restated
here.
