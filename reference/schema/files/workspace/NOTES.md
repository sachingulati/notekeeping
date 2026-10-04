---
file:        NOTES.md
scope:       workspace
schema:      1
enabled:     true
tier:        core
shape:       document
owner:       nk:init creates it; promotion writes to it
trigger:     always
authority:   original
budget:      12000 bytes
---

## Question
What do I need in every session in this workspace, whatever project I am in?

## Admission
A fact true across the projects that sit in this workspace, needed often enough to be worth standing
context. Plus a pointer to what is read on demand, and the read lines, as a project's.

## Exclusion
True of one project -> that project's NOTES.md. True whatever you are working on, in any workspace ->
global. Current work state -> `## Where things stand` in the active work item's resume.md, pointed at
and never copied. **Something to do rather than something that is true** -> `instructions.md` at this
scope, which this file's read line points at.

## Entry format
The same shape as a project's NOTES.md, the read lines included, with `<store>/` in place of
`<store>/projects/<project>/`. The workspace's read line - in `<workspace-root>/CLAUDE.local.md` -
makes **every** session under the workspace root read this file, including ones working on no
project at all.

**A write past this file's own ceiling triggers the budget notice**, per
the budget-notice rule.

**`/nk:init` creates this file with the workspace store** - the title and header only, so the
workspace read line always has a target. `/nk:save` promotes into it and maintains it after;
`/nk:adopt` writes it while building the store, and `/nk:review` can demote an entry out of it.
