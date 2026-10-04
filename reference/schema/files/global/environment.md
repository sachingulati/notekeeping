---
file:        environment.md
scope:       global
schema:      1
enabled:     true
tier:        core
shape:       register
owner:       promotion
trigger:     always
authority:   original
budget:      6000 bytes
---

## Question
What is on this machine, where is it, and how do I use it?

## Admission
A fact about your working environment that you look up when a task reaches it, and that no project
owns: an installed tool and its version, where something lives, a helper script or shell function
and how to call it, a service or server you have set up, how a drive or directory is laid out.

## Exclusion
A trap - it cost time, and you would not have predicted it -> `gotchas.md`. Why something is set up
the way it is, or why something is deliberately absent -> `decisions.md`. How you want the agent to
behave with a tool -> `instructions.md`, or your harness instructions file. Needed in most sessions
rather than when a task reaches it -> one line in `NOTES.md`, with the detail here. Specific to one
project's build or run -> that project's `runbook.md`.

## Entry format
- **The thing, and the one fact you come here for.** Version, path, or the call that uses it; what
  it is for; anything you need to know before using it. `(source - date)`
  > **Corrected <date> (source):** what the earlier version said, and what changed.

One entry per tool, location or script, named first in bold so the register scans as an inventory.
Versions and paths go stale faster than anything else in the store, so the date matters here more
than anywhere: a version with no date is a guess. Corrections are inline and never overwrite. About
50 words per entry; a procedure longer than that belongs in a work bundle or an area, and the entry
cites it. No manual numbering.

**Always loaded, so it is budgeted in bytes.** This file is imported by the global rule file,
`~/.claude/rules/notekeeping.md`, beside `NOTES.md`, and loads in every session - the user's
call, so the agent never has to think to look up where a tool lives. It is the one register with a
byte ceiling rather than an entry count, because an entry count cannot bound what is charged on
every prompt. Keep entries to the fact you come here for; a procedure goes to
a work bundle or an area and the entry cites it.
