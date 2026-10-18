# Adopt - the report

**Read this before phase 4.** What the run's report says, in which order, and how its proposal is
grouped so a large one can be read before a yes.

## The five parts

Say what you built, and what you changed. A header - the command, the store, the date, the
arguments, and the narrowing answer or that the default was taken - then five parts, in
this order:

1. **Inventory** - what was there, by count and size.
2. **What was written** - every file with its entry count, by absolute path. Group by target so the
   shape of the store is visible. **Then what the lean pass dropped, merged or moved**, each with
   its reason.
3. **What was trimmed** - every file touched, every line removed, and **the store file each one went
   to.** **Quote every removed line**, and **name the run's `copies/` folder holding the copies** - a
   revert is copying a file back. Every file that could not be trimmed, and why. If the
   trim was declined or skipped, say so.
4. **Unmapped, excluded, skipped, unplaced and contradictory** - everything that matched no target,
   every credential exclusion, every reference you could not or did not follow, every source skipped as already adopted, every entry for a
   project this store does not hold with where it belongs and the next step, every read line
   pointing at another store, every contradictory pair, and every *kept* line of a context file, each
   with a reason. This section is
   never empty by omission; if there is nothing, say so.
   **Then *Left for the next run*** - every file recorded `pending`, with what pointed at it, how
   much this run read against `adopt_max_bytes`, and that the next `/nk:adopt` continues there.
5. **What to look at first** - the targets where confidence was lowest, the unsettled pairs, and the
   `verified: unknown` count.

Every count follows the report-shape rule - one numbered
list, the count is its last number, and a split prints its arithmetic. **What you wrote and what you
removed are counted after doing them**, never from the proposal.

## The proposal is grouped by category

A first pile runs to hundreds of items, and a flat list of them cannot be read before a yes. So the
proposal's numbered list - parts 2 and 3, before anything is applied - is grouped:

| Category | One per |
|---|---|
| **A write** | target file: its scope and its name - *global · environment.md*, *workspace · gotchas.md*, *project web-client · NOTES.md*. A lean-pass verdict on an entry already there goes in the category of the file it changes |
| **A trim** | source file - *trim · ~/.claude/CLAUDE.md* - its lines in file order |
| **Nothing to write** | part 4's items, in one category with no letter |

**Each category with something to write has a letter** - `A`, `B`, … in report order, writes before
trims, global first, then `AA` past `Z`. **Items are numbered straight through, so a category's
items are one range**, and a number means the same in every view. The terminal shows one line per
category - its letter, its name, its item count and its range - then the arithmetic, *620 items - 540
to write (A-J), 60 to trim (K-M), 20 with nothing to write*, and the run's id. Every item is in the
run's `report.md`; *Review as a page* and *Show here* are how they are read.

The page is the run's report as the report-pages rule carries it - the
first question's *Review as a page*.
