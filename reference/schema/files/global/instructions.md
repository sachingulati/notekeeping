---
file:        instructions.md
scope:       global
schema:      1
enabled:     true
shape:       document+append
owner:       promotion
trigger:     always
authority:   original
budget:      5000 bytes
---

## Question
What must I *do* in every session, wherever I am working?

## Admission
The user asked for this to be standing behaviour, and it holds across every workspace rather than in
one of them. **An instruction is obeyed, not looked up**, so it carries no correction shape and
lives outside the registers.

## Exclusion
True of one workspace -> that workspace's `instructions.md`. True of one project -> that project's.
A fact that is true -> `NOTES.md`, or the register it belongs to.

## Entry format
The same shape as a project's `instructions.md`, and **the same budget** - 5000 bytes, as at every
scope that admits an instruction.

This file is imported by the global rule file, `~/.claude/rules/notekeeping.md`, and is charged in
**every** session, including sessions that touch no store. That reach is the reason an entry here must hold everywhere: one that holds in a single
workspace is obeyed in all the others too.

**Nearest scope wins where two levels disagree**, and `/nk:doctor` reports the pair rather than
resolving it - the same rule as between a project and its workspace. It also reports an entry here
that contradicts the harness instructions file, and never edits either.
