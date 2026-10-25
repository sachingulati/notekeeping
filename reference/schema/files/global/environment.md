---
file:        environment.md
scope:       global
schema:      1
enabled:     true
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
owns: an installed tool and the version that constrains work, where something lives, a helper script or
shell function and **when to reach for it**, a service or server you have set up, how a drive or
directory is laid out. **What its own file already says is not copied** - an alias's body, a
config's values, a README's usage - per *Worth recording* in the promotion rule.

## Exclusion
A trap - it cost time, and you would not have predicted it -> `gotchas.md`. Why something is set up
the way it is, or why something is deliberately absent -> `decisions.md`. How you want the agent to
behave with a tool -> `instructions.md`, or your harness instructions file. Needed in most sessions
rather than when a task reaches it -> one line in `NOTES.md`, with the detail here. Specific to one
project's build or run -> that project's `runbook.md`.

## Entry format
- **The thing, and the one fact you come here for.** Version, path, or the call that uses it; what
  it is for; anything you need to know before using it.
  > **Corrected <date>:** what the earlier version said, and what changed.

One entry per tool, location or script, named first in bold so the register scans as an inventory.
Versions and paths go stale faster than anything else in the store, and a version
the machine reports when asked is not stored at all (*Admission*). Corrections are inline and never overwrite. About
50 words per entry; a procedure longer than that belongs in a work bundle or an area, and the entry
cites it. No manual numbering.

**Always loaded, so it is budgeted in bytes.** This file is imported by the global rule file,
`~/.claude/rules/notekeeping.md`, beside `NOTES.md`, and loads in every session, so the agent
never has to look up where a tool lives. It is the one register with a
byte budget rather than an entry count, because an entry count cannot bound what is charged on
every prompt. Keep entries to the fact you come here for; a procedure goes to
a work bundle or an area and the entry cites it.
