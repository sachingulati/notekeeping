# Adopt - what it writes, what moves, and the lean pass

**Read this before phase 2.** Which files a run writes into the store, where a fact goes and where
an instruction goes, and the lean pass over the result as a whole.

## What it writes into the store

Registers and ledgers, and the two project documents - `gotchas.md`, `patterns.md`,
`decisions.md`, `domain.md`, `runbook.md`, `architecture.md`, `process.md`, global's
`environment.md`, each scope's `NOTES.md` and `instructions.md`, and a project's `overview.md` where
the material supports them.

Write only what the material earns. A target with no admitted entry is not created: an empty
register is a stub, and a stub looks answered. `/nk:init` follows the same rule, beyond the files a
store or project needs in order to exist.

Check the target before writing. An entry whose text already matches one already in the store is
skipped, not duplicated - a second `/nk:adopt` pass over the same material adds only what is new.

Every entry keeps the format its definition specifies, and gains no source or date. Where a
`verified:` stamp cannot be established from what you read it is `verified: unknown` - visible,
never guessed.
A write that leaves a file with a numeric `budget:` at or past `budget_notice_pct` of it says so, per
the budget notice.

Then `index.md` per the index shape and the index-writing rule if any work item was
written. This command writes no read line: registration does, and the notes it builds are what the lines point at.

## What moves, and what stays

Routing a candidate - admission, scope, already there - is per the promotion rule.

Both move, and they go to different files. A fact you *look up* - a port, a name, a trap, a
decision and its reason - goes to `NOTES.md` or the register it belongs to. A directive you *obey* -
house style, a workflow rule, *always run the linter* - goes to the `instructions.md` of the scope it
holds at: the project, the workspace, or global for one that holds everywhere. **Never merge the
two**: a fact filed as an instruction becomes unfalsifiable, and an instruction filed as a fact
becomes optional. The test is the schema's own (the global `NOTES.md` definition).

**A line that holds both is split.** *`jq` is installed; use it for every JSON edit* is a fact
and a directive: the fact goes to its register, the directive to `instructions.md`, each in the source's
own words. **A split separates; it never rewords** - a reworded rule is a different rule, and nobody
approved it.

**An instruction is a directive the agent obeys, and nothing else is.** A fact about a tool, a
permission the user granted, a description of the file it came from, a choice and its reason - none
is an instruction, whatever file it was in or however it is phrased.

**An instruction that holds only when a task reaches it is not loaded always.** Five rules for
cutting a release apply only when releasing. Its detail goes to the on-demand register it belongs
to, and `instructions.md` keeps one trigger line - *before cutting a release, read `<file>`* - which the report shows beside the lines it replaces. **An instruction found under a
`<path>` was never in force**, since nothing loaded it: it is proposed, never applied under `all`,
and the report says that applying it changes how the agent behaves.

**The budget does not bind the run.** Every instruction from a context file is placed, and the lean
pass, not the budget, decides what earns a line. The write says so per the budget notice, above,
and the report names each file it left past its budget.

## The lean pass

**After classifying, read the result as a whole** - each target file as it would stand, its present
entries included, and every file beside it - and apply
the promotion rule, *Worth recording*, to every entry. Per entry it can
**keep** it, **drop** it, **merge** it with another that says the same, or **move** it to an
on-demand register with a trigger line. Then *Organising as it writes*, in the same rule: **merge**
entries that each say part of one thing, and **group** every topic of three or more entries into its
area. Each verdict that is not *keep* is in the report with its reason, in a section of its own.

**Looking at the whole is the point.** Per-entry admission can pass every entry while the result
fails - a file past its budget, one fact in two files, five lines where one trigger line would do.
Only a pass over the whole file sees that.

**A dropped line is trimmed like a moved one** - shown, quoted, covered by the same yes, and copied
whole into the run's `copies/` first. Left in its context file it would go on loading in every session, which is
what dropping it was for.
