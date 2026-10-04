---
file:        secrets.md
scope:       workspace
schema:      1
enabled:     true
tier:        extended
shape:       register
owner:       nk:save, only on the user's request
trigger:     the user asks for a credential to be kept
authority:   original
budget:      none - read on demand, never projected
---

## Question
What do I log in with across this workspace's projects? Read it before asking the user for a login
or a key.

## Admission
A credential the user asked, in this session, to have kept, used by more than one project here -
exactly as at project scope.

## Exclusion
One project's -> that project's `secrets.md`. Used whatever you work on, in any workspace ->
global's.

## Entry format
The same shape as a project's `secrets.md`, and the same rules: read only to use a value, never
quoted, and the first write adds its `.gitignore` line and its `## Read on demand` line in the
same pass.
