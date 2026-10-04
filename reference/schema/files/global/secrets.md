---
file:        secrets.md
scope:       global
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
What do I log in with, whatever I am working on? Read it before asking the user for a login or a
key.

## Admission
A credential the user asked, in this session, to have kept, not tied to one workspace or project - a
machine account, a personal token - exactly as at project scope.

## Exclusion
One project's -> that project's `secrets.md`; one workspace's -> the workspace's. Where a tool lives
and how to call it -> `environment.md`, the entry naming this file rather than the value.

## Entry format
The same shape as a project's `secrets.md`, and the same rules: read only to use a value, never
quoted, and the first write adds its `.gitignore` line and its `## Read on demand` line in the
same pass.
