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
budget:      4000 bytes
env_axis:    optional
---

## Question
What do I need in every session, whatever I am working on?

## Admission
A fact true of your working life rather than of any codebase, needed often enough to be worth
standing context.

## Exclusion
True of one project -> that project's NOTES.md. An instruction about how to behave rather than a fact
that is true -> the `instructions.md` of the scope it actually holds at - work, area, project or
workspace - and **where it holds everywhere, your harness instructions file, not here**.

## Entry format
The same shape as a project's NOTES.md, with a tighter ceiling.

**Global has no projection, and needs none.** `~/.notekeeping/` is an ancestor of every workspace, so
delivering it automatically would mean a `CLAUDE.local.md` in the home directory - charged in every
session on the machine, including every session that touches no store at all. This file is read on
demand. The ceiling is tight because what earns a place here is charged against attention rather
than context: it is the file consulted from anywhere, so a fact that is not true everywhere makes
every other entry less trustworthy.

Instructions are not knowledge. This file does not replace or absorb your harness instructions file;
one narrow class moves here - facts in that file that are *looked up* rather than *obeyed*, which are
knowledge paying always-loaded cost with no append point.

**There is no `instructions.md` at global scope, and the reason is delivery rather than taste.**
Every other scope that admits one is projected: work through `/nk:load`, area through the entry that
names it, project and workspace through their `CLAUDE.local.md`. **Global has no projection and is
not getting one** - it would mean `~/CLAUDE.local.md`, charged in every session on the machine,
including every session that touches no store at all. An instruction stored here would be read on
demand, which for an instruction means obeyed when someone happens to look: stored, and not in
force. **Your harness instructions file already always-loads**, so the mechanism exists and is
better than the one we would be building. `/nk:save` **reports** a global-shaped instruction it
cannot place and names that file; it never absorbs one.
