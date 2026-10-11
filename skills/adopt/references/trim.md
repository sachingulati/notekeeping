# Adopt - every line's verdict, and the trim

**Read this when the inventory holds a context file, or a report being applied holds a trim item.**

## Every line of a context file gets a verdict

**A context file is accounted for whole, not only the lines that classified.** Each of its lines -
a heading and blank lines go with the section they belong to - gets exactly one verdict:

| Verdict | When | Reported with |
|---|---|---|
| **moved** | its content was written into the store, or is already there | the store file it went to |
| **dropped** | it fails *Worth recording* - derivable, one-off, generic, said elsewhere, about the record itself - or it is **outdated** | the reason |
| **kept** | this plugin's read line, a credential, an entry for a project this store does not hold, or a line you cannot place with confidence | the reason |

**Outdated is shown, never guessed** - settled as a contradiction is for `adopt`: by the material,
by the store, or by one light read - a path that does not exist, a command or version the material
or the store contradicts. Unsure is *kept*.

**Moved and dropped lines are trim items**, under the trim invariant and the trimmability table
below; a file that cannot be trimmed has them quoted as yours to remove. **The verdicts add up to
the file's lines**, printed as arithmetic per file - *42 lines - 30 moved, 8 dropped, 4 kept* - so
nothing is left behind unreported.

## The trim invariant

**A line is removed only when its content is in the store, and the report names the file it went
to - or when the lean pass dropped it, and the report gives the reason.** A trim finishes a move or
a drop the yes covered, and the trim copy keeps every line it removed. A line *kept* - anything
you could not place with confidence among them - stays exactly where it is.

Whether a file can be trimmed depends on where it is, and git decides it:

| Where the file is | Trimmed? |
|---|---|
| **In no work tree** - `~/.claude/CLAUDE.md` on most machines, a workspace root that is no repository | **yes** |
| **In a work tree**, named `CLAUDE.md` or `CLAUDE.local.md`, and **ignored** | **yes** |
| **In a work tree**, anything else | **never** - the report quotes the lines that moved or were dropped and says the file is yours to trim |

The work tree is the repo-facts rule's *the root*, run on the file's own directory: *not a git
repository* is no work tree. **Ignored** is its *ignored*, run on the file - git's own answer, every
ignore file and pattern included. A file committed and later ignored is trimmed: in the working tree,
where the diff shows it. **Git unavailable: the file is not trimmed** - the lines are quoted and the
report says git could not be asked.

**A read that is refused, or a walk that cannot finish, makes the file untrimmable** - say which, and
why. Never trim on a guess.

**The trim copy comes first.** Before the first line of a file is removed, copy it - the file-facts
rule's *an exact copy*, byte for byte - into `.notekeeping/tmp/adopt-<YYYYMMDD>-<n>/` - the same `<n>` as this run's report - at its path from its
root: `home/` for a file under `~`, `ws/` under the workspace root, `repo-<name>/` under a registered
repository, whichever is nearest. **The report's trim section names the folder and says a revert is
copying a file back.** No skill empties or deletes these copies. **The report quotes every line it
removes** as well - the copy is the way back, the quote is what the yes was given to.

## The trim is shown in full, and the same yes covers it

Everything else is a copy into a directory you can delete; this is the one thing that changes a file
you wrote. Show the lines, grouped by file, inside the proposal - never a count in their place -
so the yes that approves the store has seen them, and with each file's trimmability from the
table above. Leaving it out is a normal answer: *Apply* builds the store and removes nothing, and
*Trim only* or `--apply <report> <trim items>` trims later from the same saved report.
