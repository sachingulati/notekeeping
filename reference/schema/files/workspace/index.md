---
file:        index.md
scope:       workspace
schema:      1
enabled:     true
tier:        core
shape:       bookkeeping
owner:       nk:save
trigger:     every save that touches a work item; /nk:index on demand
authority:   derived
budget:      none
---

## Question
Which work items does this store hold, and how is one found without opening it?

## Admission
One row per work item under `work/`, and only the columns the index shape admits - what a command
resolves *by*, plus what is needed to choose between candidates.

## Exclusion
A field nothing resolves by -> it stays in `requirements.md`, found by grep. A work item's state ->
its `resume.md`. Anything written by hand -> lost at the next rebuild: this file is derived whole.

## Entry format
**The index shape is the one home of its columns, its order and its rendering**; this definition
does not restate them. **Derived whole from the tree** - `requirements.md` frontmatter and the folder
path - so it is never migrated: a release that changes its shape is met by the next rebuild, which
writes the new shape. `/nk:save` regenerates it; `/nk:index` forces a rebuild for repair.
