# `--apply`

**Applyable means there is a store write to make and you are allowed to make it - eight of the ten.**
1 merge - 2 split - 3 area - 5 retire - 6 demote - 7 promote - 9 close - 10 demote from `NOTES.md`.

**4 and 8 are not withheld, they have nothing to write.** 4's proposal is to *run a check*, which
the user runs or asks for - recording *what was checked and what it returned* without running it
would mean inventing it. 8's proposal **is** the flag: applying it
would mean writing the reversal, which you never do. Report both as proposals under every flag.

### `--apply` applies a run

**A pass with a finding is a run item**, and `--apply [<run>] [all | <numbers>]` applies it, per
the report-apply rule's `## --apply`. **A pass never applies its own findings on a flag** - `--apply` reads a
run that already exists, the latest one when none is named, and **the pass does not run again**:
the findings are the report's, so a number means what it meant when it was printed. The finding set is
not stable between identical passes, which is exactly why the report and not a new pass is what gets
applied.

**A re-entry is not a pass.** When the conversation holds this skill's report question and an
answer typed after it, and that run still has an open finding, read its `report.md`, write each
answer onto its finding, show each finding's resolved write, and ask the report question again - the
report-apply rule's re-entry, with the findings as the items.

**The walk - `--apply` with no selection** - takes the report's applyable findings in order, shows
each diff, and asks with one `AskUserQuestion` per finding: *apply*, *skip*, *decline* or *stop*.
*Stop* keeps what was applied. **A skipped finding is not resolved** - the next pass finds it again;
**a declined one is**, and the next pass carries it. Nothing is written into the store for either:
the answer lives on the run. **The terminal report still lists it**: *applied 3 of 8, skipped 4,
declined 1, 0 stale*, counted from `log.md` and the states.

### When there is no turn to answer in

**Write nothing, and keep the run.** Say which findings were applyable and that nothing was written
because no selection was made: a selection is an input you cannot supply on the user's behalf. The run is what lets someone make it later.

### Under every form

1. **Never delete.** Retirement marks an entry superseded and leaves its text where it is. A merge
   keeps both entries' corrections. A split moves entries into `areas/<topic>/` and leaves a pointer.
2. **A split creates the area** - its files, the pointer, its row in `areas/INDEX.md`, and
   `NOTES.md`'s area lines where the index is new - per the promotion rule's *Organising as it
   writes*. For findings 2 and 3 alike. Nothing else needs re-rendering.
3. **`--apply` is never implied by another flag**, and `--dry-run` beats it: with both, show
   everything, offer nothing and write nothing.
4. **Apply nothing outside the store**, and nothing the report does not carry.
