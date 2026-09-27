---
doc:   report-shape
title: How a report states what it found, so a count cannot disagree with a list
---

# Report shape

**Every command that reports a set of things - findings, proposals, repairs, conversions - states
how many.** That number has disagreed with the list beside it **three times in recorded runs**, and
every one of those runs was made by a build that already said *never report a count you did not
derive*. **The instruction is not the remedy**, because it asks for care at the exact moment care is
scarce. This file replaces it with a shape in which the disagreement has nowhere to live.

## The three rules

**1 · One numbered list, and everything reported is in it.**

Number the items as you print them, from `1`. **Nothing that counts as a finding is printed outside
the list** - not in a preamble, not in a paragraph before it, not as an aside afterwards. The
recorded failure was exactly this: a contradiction finding was described two paragraphs above the
list, so it was reported but never enumerated, and the summary then counted the list.

**2 · The count is the last number you printed. It is never formed separately.**

There is no second act of counting to get wrong. If the list ends at `11`, the answer to *how many*
is `11`, read off rather than totalled. **A number arrived at any other way is not a count**, however
carefully it was added up.

**3 · A split into categories prints its arithmetic, and it reconciles to that number.**

Never `8 are applyable and 2 are report-only` beside a list of eleven. Write the sum:

```
11 findings - 8 applyable, 3 with nothing to write
```

**The parts must add to the whole, visibly.** Where they do not, the error is in the categories and
the list is right - **re-derive the categories, and never adjust the total to match them.**

## A yes applies what was shown

**A proposal and the write that follows it are one decision.** Where a command shows what it would
write and there is a turn to answer in, it ends with one question - *apply this?* - and **a plain yes
is the go-ahead.** Nobody re-runs the command with a flag to act on what they just read.

1. **What was approved is what gets written** - every item on the list, and nothing that was not on
   it. The write carries out the proposal on the screen, in the same invocation and from the same
   reading. **Nothing is classified, routed or numbered again**: a second derivation can land
   somewhere defensible and different, and then what was approved and what was written are two
   things nobody reconciled.
2. **An answer can narrow it** - *"yes, but not 4"*, *"only the store"* - and the narrowed list is
   then what gets written. It never adds to it.
3. **Before the first write, check that what the proposal read has not moved** - the files it
   quoted, the counts it printed. Where something has, write nothing: say what moved, and propose
   again. This checks that the approval still describes the material; it is not a second decision.
4. **No turn to answer in means nothing is written.** The report is saved (below), and a later
   `--apply` is how it gets applied. **No flag approves a proposal before it exists.**
5. **`--dry-run` shows the proposal, does not ask, and saves no report.**

## The report is saved, and `--apply` applies it

**`review`, `adopt` and `upgrade` save every proposal they make**, and `--apply` acts on a saved
report - never on a proposal that has not been made yet. The report is what someone could read; the
write carries it out. That is rule 1 above across a gap in time: nothing is classified, routed or
numbered again, because the numbers are in the file.

**Where it goes.** `.notekeeping/tmp/<command>-<YYYYMMDD-HHMMSS>.md`, written before the question is
asked, and named on the last line of the report - under `--oneline`, in the outcome line. The
directory is created by the first report, together with a `tmp/.gitignore` holding `*`. **A report
is output, not store content**: writing one never counts as changing the store.

**What it holds.** The report as printed - one numbered list per the rules above - and, for each
item, **the exact write it would make** and **the text it read to propose it**, so the item can be
checked against the store later. A header names the command, the store, the time and the arguments
the run was given.

**`--apply [<report>] [all | <numbers> | <text>]`:**

| Part | Means |
|---|---|
| `<report>` | a report's file name or path. **Omitted: the latest report this command saved in this store** |
| `all` | every applyable item in the report |
| `<numbers>` | those items - `1,3,7`. A number is read against the report, so it cannot drift |
| `<text>` | narrows within the report where the command accepts it - *"only the gotchas"*. It never reaches past the report |
| nothing | walk the report's applyable items one at a time: show each write, then take *apply*, *skip* or *stop*. **With no turn to answer in, a walk cannot start** - apply nothing, and say `all` or numbers are needed |

**Say which report you are applying before anything else** - the first line, before any check or
write: its file, when it was saved, the arguments that run was given, and how many items it holds.
*"Applying review-20260928-143012 - saved today 14:30, `/nk:review web-client`, 14 findings, 9
applyable."* Whoever typed `--apply` - especially with no report named - sees which run they are
approving before it is carried out. Under `--oneline` the report's file goes in the outcome line.

**No saved report, or the one named does not exist?** Say so, name the bare command that makes one,
and write nothing.

**Before each write, check the item against the store** - rule 3, per item. Where the text it read
has changed since the report was saved, **skip that item and say it went stale**; never apply it
blind. Applying a report twice is therefore safe: what was already written no longer matches.

**After applying, append `## Applied` to the report** - the date and the items written, skipped and
stale. `/nk:doctor` deletes reports that have been applied, or are more than a week old, and never
today's.

## What this does not cover

**A count of things you did rather than things you found** - files converted, lines removed, entries
written - is counted **from what you actually did**, after doing it. Never from the plan, never from
the proposal, and never from the survey that preceded it: the gap between *proposed* and *done* is
the thing such a number exists to expose.

**A count of things you did not read** - a survey's `outstanding`, a skipped definition - is derived
from the glob or the grep that produced it, and the report says which. It is evidence that a pass
cost what it should have, so an estimate defeats its whole purpose.

## Why the shape rather than the warning

**A step the model can skip is a step it will sometimes skip** - as when a
confirmation was replaced by a selection because a selection is an input that cannot be
manufactured. **A total is the same kind of hazard**: stated separately, it is a claim nobody
checks against the thing it summarises. Made the last line of the list, it is not a claim at all.
