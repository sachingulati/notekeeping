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

**This file is imported by the global rule file, `~/.claude/rules/notekeeping.md`**, and loads in
every session - including every session that touches no store at all (the projection rule). That reach is what earns a place here: a fact
that is not true everywhere is delivered everywhere anyway, and makes every other entry less
trustworthy.

Instructions are not knowledge, and they have their own file here: `instructions.md`, imported
beside this one - so this file carries **no read line to it**, only the two area lines, where
`areas/INDEX.md` exists. Neither file replaces or absorbs your harness
instructions file; `/nk:adopt` moves both out of it - facts that are *looked up* rather than
*obeyed* come here, and what you must *do* goes to `instructions.md`, whose exclusion states the
trim rule.

**`/nk:save` writes this file when promotion first reaches it, and maintains it after.**
`/nk:adopt` writes it while building the store, and `/nk:review` can demote an entry out of it.
