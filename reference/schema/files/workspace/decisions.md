---
file:        decisions.md
scope:       workspace
schema:      1
enabled:     true
tier:        core
shape:       ledger
owner:       promotion
trigger:     always
authority:   original
budget:      none - a ledger is never trimmed
env_axis:    optional
---

## Question
What was chosen for this workspace, why, and what would reopen it?

## Admission
A choice that binds more than one project here - a toolchain, a convention, a shared constraint - with
its reason and the condition that would reopen it.

## Exclusion
Binds one project -> that project's decisions.md. A choice about how you work whatever you are working
on -> global. A trap rather than a choice -> gotchas.md.

## Entry format
- **The decision, in one line.** Why, in one or two. **Would reopen if:** the condition that would
  make you revisit it. `(source - date)`

**A ledger is never edited.** A decision that changes gets a **new** entry that supersedes the old one
by name and date; both stay visible, because the reasoning is worth more than the conclusion.
`Would reopen if:` is required - an entry without it records what was chosen and loses why, which is
the half that ages well.
