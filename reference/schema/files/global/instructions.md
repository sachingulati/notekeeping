---
file:        instructions.md
scope:       global
schema:      1
enabled:     true
tier:        core
shape:       document+append
owner:       promotion
trigger:     always
authority:   original
budget:      2000 bytes
---

## Question
What must I *do* in every session, wherever I am working?

## Admission
The user asked for this to be standing behaviour, and it holds across every workspace rather than in
one of them. **An instruction is obeyed, not looked up**, so it carries no correction shape and
lives outside the registers.

## Exclusion
True of one workspace -> that workspace's `instructions.md`. True of one project -> that project's.
A fact that is true -> `NOTES.md`, or the register it belongs to. `/nk:adopt` moves both facts and
instructions out of your harness instructions file (`~/.claude/CLAUDE.md`) into global - never
merged, and never written into. Its own trim is the one exception to `--apply all`: a line only
comes out when named by number, because the harness instructions file is the user's own.

## Entry format
The same shape as a project's `instructions.md`, and **the same ceiling** - 2000 bytes, as at every
scope that admits an instruction.

This file is imported by the global rule file, `~/.claude/rules/notekeeping.md`, and is charged in
**every** session, including sessions that touch no store. That reach is the reason an entry here must hold everywhere: one that holds in a single
workspace is obeyed in all the others too.

**Nearest scope wins where two levels disagree**, and `/nk:doctor` reports the pair rather than
resolving it - the same rule as between a project and its workspace. It also reports an entry here
that contradicts the harness instructions file, and never edits either.
