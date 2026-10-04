# `--apply`

**Applyable means there is a store write to make and you are allowed to make it - eight of the ten.**
1 merge - 2 split - 3 area - 5 retire - 6 demote - 7 promote - 9 close - 10 demote from `NOTES.md`.

**4 and 8 are not withheld, they have nothing to write.** 4's proposal is to *run a check*, and your
tools are the ones declared above - you cannot run an arbitrary check, and recording *what
was checked and what it returned* would mean inventing it. 8's proposal **is** the flag: applying it
would mean writing the reversal, which you never do. Report both as proposals under every flag.

### `--apply` applies a saved report

**Every pass saves its report**, and `--apply [<report>] [all | <numbers>]` applies it, per
the report shape's *The report is saved*. **A pass never applies its own findings on a flag** - `--apply` reads a
report that already exists, the latest one when none is named, and **the pass does not run again**:
the findings are the report's, so a number means what it meant when it was printed. The finding set is
not stable between identical passes, which is exactly why the report and not a new pass is what gets
applied.

**A re-entry is not a pass.** When the conversation holds this skill's report question and an
answer typed after it, and that report has no `## Applied`, read the report, append the answer as
`## Answers` keyed by finding number, show each finding's resolved write, and ask the report question
again - the report shape's rules, with the findings as the items.

**The walk - `--apply` with no selection** - takes the report's applyable findings in order, shows
each diff, and asks with one `AskUserQuestion` per finding: *apply*, *skip* or *stop*. *Stop* keeps what was applied. **A skipped finding is
not resolved**: nothing is written into the store recording the decline - the next pass finds it
again, which is correct, since declining a proposal is not answering it. **The terminal report still
lists it**, per the report shape's `## Applied`: *applied 3 of 8,
skipped 5, 0 stale*, counted from what you wrote.

### When there is no turn to answer in

**Write nothing, and save the report.** Say which findings were applyable and that nothing was written
because no selection was made: a selection is an input you cannot supply on the user's behalf. The report is what lets someone make it later.

### Under every form

1. **Never delete.** Retirement marks an entry superseded and leaves its text where it is. A merge
   keeps both provenance stamps. A split moves entries into `areas/<topic>/` and leaves a pointer.
2. **A split writes the area and its row in `areas/INDEX.md`.** For findings 2 and 3 alike - the row
   is what points a session at the area. **Where `areas/INDEX.md` did not exist, the same pass adds
   `NOTES.md`'s two area lines**, per the `NOTES.md` definition: an index nothing reads is never
   read. Nothing else needs re-rendering.
3. **`--apply` is never implied by another flag**, and `--dry-run` beats it: with both, show
   everything, offer nothing and write nothing.
4. **Apply nothing outside the store**, and nothing the report does not carry.
