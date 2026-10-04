---
doc:   report-shape
title: How a report states what it found, so a count cannot disagree with a list
---

# Report shape

**Every command that reports a set of things - findings, proposals, repairs, conversions - states how
many, in this shape**, so the count can never disagree with the list beside it.

## The three rules

**1 · One numbered list, and everything reported is in it.**

Number the items as you print them, from `1`. **Nothing that counts as a finding is printed outside
the list** - not in a preamble, not in a paragraph before it, not as an aside afterwards.

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

**A proposal and the write that follows it are one decision, and the saved report carries it.** What
was shown is the report's resolved list - every item, with the answer each was given - and that list
is what gets written. Nobody re-runs the pass to act on what they just read.

**The question.** After the report is saved (below), ask once, with `AskUserQuestion`:
**Apply / Apply and publish / Change answers / Not now.** *Apply and publish* appears only where the
skill offers its report as a page. An item with a question of its own - *merge into which entry?* -
shows it in the report, numbered with its item, before the question.

| Pick | Do |
|---|---|
| **Apply** | read the report - its items and its `## Answers` - **never the conversation**; check each item before its write (below); append `## Applied` |
| **Apply and publish** | *Apply*, then publish the report as a page |
| **Change answers** | ask again, the current answers shown |
| **Not now** | the report stays. Where the skill has `--apply`, a later `--apply` applies it; where it does not, a later run starts fresh |

**An answer typed rather than picked is not applied in the turn it arrives in** - *yes*, *yes but
not 4*, *3: merge into auth* alike. The skill is started again with its original arguments, and that
run is a **re-entry** when the conversation holds this skill's question and an answer after it, **and
the report that question named has no `## Applied`**. That report is the open one. Otherwise the run
is fresh, and passes again.

**On a re-entry there is no pass.** Read the open report; append the answers to it as
**`## Answers`**, keyed by item number - *3: merge into auth*, *5: skip*, and a plain *yes* as one
line per applyable item; show each item's resolved write; then ask the question again. A second
re-entry appends to the same section, and the later answer to an item wins.

1. **What was approved is what gets written** - every item the answers leave in, and nothing that was
   not in the report. **Nothing is classified, routed or numbered again**: a second derivation can
   land somewhere defensible and different, and then what was approved and what was written are two
   things nobody reconciled.
2. **An answer can narrow the list** - *"yes, but not 4"*, *"only the store"* - and the narrowed
   list is then what gets written. **It never adds to it.**
3. **Before the first write, check that what the proposal read has not moved** - the files it
   quoted, the counts it printed. Where something has, write nothing: say what moved, and propose
   again. This checks that the approval still describes the material; it is not a second decision.
4. **No turn to answer in means nothing is written.** The report is saved, and a later `--apply` is
   how it gets applied. **No flag approves a proposal before it exists.**
5. **`--dry-run` shows the proposal, does not ask, and saves no report.**

## The report is saved, and `--apply` applies it

**`review`, `adopt` and `upgrade` save every proposal they make**, and `--apply` acts on a saved
report - never on a proposal that has not been made yet. **`/nk:doctor` saves one too, every run, but
it has no `--apply`** - `--fix` acts on what the same run just found, immediately. The report is what
someone could read; the write carries it out. That is rule 1 above across a gap in time: nothing is
classified, routed or numbered again, because the numbers are in the file.

**Where it goes.** `.notekeeping/tmp/<command>-<YYYYMMDD>-<n>.md`, written before the question is
asked, and named on the last line of the report - under `--oneline`, in the outcome line. The
directory is created by the first report, together with a `tmp/.gitignore` holding `*`. **A report
is output, not store content**: writing one never counts as changing the store.

**The name has a date and a counter, never a time.** The date is the session's; there is no clock to
read, so a time would be invented. `<n>` is one more than the highest this command already has in
`tmp/` for that date - `tmp/<command>-<YYYYMMDD>-*.md`, by Glob - and `1` when there is none. **A
report is never overwritten**: where the name turns out to be taken when writing, take the next
number. The report holds no time anywhere.

**What it holds.** The report as printed - one numbered list per the rules above - and, for each
item, **the exact write it would make** and **the text it read to propose it**, so the item can be
checked against the store later. A header names the command, the store, the date and the arguments
the run was given. A re-entry adds `## Answers`; an apply adds `## Applied`. Nothing else
changes in it after it is saved.

**`--apply [<report>] [all | <numbers> | <text>]`:**

| Part | Means |
|---|---|
| `<report>` | a report's file name or path. **Omitted: the latest report this command saved in this store** - the latest date, then the highest `<n>` |
| `all` | every applyable item in the report |
| `<numbers>` | those items - `1,3,7`. A number is read against the report, so it cannot drift |
| `<text>` | narrows within the report where the command accepts it - *"only the gotchas"*. It never reaches past the report |
| nothing | walk the report's applyable items one at a time: show each write, then ask with one `AskUserQuestion` per item - *apply*, *skip* or *stop*. **With no turn to answer in, a walk cannot start** - apply nothing, and say `all` or numbers are needed |

**Say which report you are applying before anything else** - the first line, before any check or
write: its file, when it was saved, the arguments that run was given, and how many items it holds.
*"Applying review-20260928-2 - saved today, `/nk:review web-client`, 14 findings, 9
applyable."* Whoever typed `--apply` - especially with no report named - sees which run they are
approving before it is carried out. Under `--oneline` the report's file goes in the outcome line.

**No saved report, or the one named does not exist?** Say so, name the bare command that makes one,
and write nothing.

**Before each write, check the item against the store** - rule 3, per item. Where the text it read
has changed since the report was saved, **skip that item and say it went stale**; never apply it
blind. Applying a report twice is therefore safe: what was already written no longer matches.

**After applying, append `## Applied` to the report** - the date and the items written, skipped and
stale. `/nk:doctor --fix` empties reports that have been applied, or are more than a week old, and
never today's. **An empty report is a removed one**: `--apply` says so and applies nothing.

## What this does not cover

**A count of things you did rather than things you found** - files converted, lines removed, entries
written - is counted **from what you actually did**, after doing it. Never from the plan, never from
the proposal, and never from the survey that preceded it: the gap between *proposed* and *done* is
the thing such a number exists to expose.

**A count of things you did not read** - a survey's `outstanding`, a skipped definition - is derived
from the glob or the grep that produced it, and the report says which. It is evidence that a pass
cost what it should have, so an estimate defeats its whole purpose.
