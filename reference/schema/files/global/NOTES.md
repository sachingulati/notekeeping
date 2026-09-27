---
file:        NOTES.md
scope:       global
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
What do I need in every session, whatever I am working on?

## Admission
A fact true of your working life rather than of any codebase, needed often enough to be worth
standing context.

## Exclusion
True of one project -> that project's NOTES.md. A tool, path, version or script you look up when
a task reaches it -> `environment.md`, with at most one line here. An instruction about how to behave rather than a fact
that is true -> the `instructions.md` of the scope it actually holds at - work, area, project,
workspace or global.

## Entry format
The same shape as a project's NOTES.md, and the same 12000-byte ceiling.

**This file is projected into `~/CLAUDE.local.md`**, and loads in every session whose directory is
under the home directory - including every session that touches no store at all
(`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`). That reach is what earns a place here: a fact
that is not true everywhere is delivered everywhere anyway, and makes every other entry less
trustworthy.

Instructions are not knowledge, and they have their own file here: `instructions.md`, rendered
beside this one under `## Standing instructions`. Neither file replaces or absorbs your harness
instructions file; one narrow class moves here - facts in that file that are *looked up* rather than
*obeyed*, which are knowledge paying always-loaded cost with no append point.
