---
doc:   migration
title: How /nk:upgrade converts a store, and how a migration is written
---

# Migration

**Only `/nk:upgrade` migrates** - the schema-version rule, which also holds the release number, the four answers and the stamp's form. This file is what that
command and `/nk:doctor` need beyond it: the stamp's edge cases, how a migration is written, and how
outstanding files are found without reading the store.

## The file stamp, in full

**The form, where it goes and how it is searched for are the schema-version rule's.** A stamp a
migration just wrote, in a file whose other lines end `\r\n`, is exactly the one an end-anchored
search cannot find again - **never rewrite a file's line endings to make a search work**; widen the
search. **A release-1 store needs no stamps written into it**: a file gains one from the first
migration that touches it.

**The stamp is written in the same write as the change it records - never in a second pass.** A file
is read, converted and stamped in one write, so a file is either unconverted and unstamped, or
converted and stamped. There is no third state, and that is what makes an interrupted migration
resumable rather than ambiguous.

**A stamp above its definition's `schema:` excludes that file from migration**, because the
outstanding test is the only test. That is correct when the plugin is simply older than the store - and it is a
hazard when a stamp was raised by hand, which silently freezes one file at a shape nothing will ever
convert. **`/nk:doctor` reports it and never repairs it**: lowering a stamp schedules a rewrite of
the user's own file, which is not a repair anyone can make on their behalf.

**Projections, `index.md` and `config.md` carry no stamp** (the schema-version rule): regenerate
them after a migration; never convert them. A derived file that is migrated rather
than rebuilt can disagree with the source it is derived from.

## The history

| Release | What changed | Definitions it moved |
|---|---|---|
| **1** | the initial shape | all of them, at birth - every definition ships at `schema: 1` |
| **2** | `overview.md`'s fields gain a bold label | **one** - `project/overview.md`. Every register and every ledger is untouched, because an entry's file and path already name it |

## Writing a migration

**Raise `schema:` on the definitions whose shape changed, and only those.** That bump *is* the
declaration of what the release touches; nothing else records it.

**The conversion lives with the definition, in a `## Migration` section**, keyed by the step it
performs:

```markdown
## Migration
**1 -> 2** - `sources:` becomes a list. A header carrying one source string becomes a single-item
list. A header with no `sources:` at all is left alone and named in the report.
```

**A definition whose `schema:` is above 1 must carry a `## Migration` section covering every step up
to it.** Without one, a file is known to be outstanding and there is no way to convert it.
`/nk:doctor` reports a missing one as an error, exactly as it does a missing `## Exclusion`.

**Keeping the conversion beside the shape is what makes the overlay work.** A user's overlaid
definition carries its own `schema:` and its own `## Migration`, resolved through
the file-definition rule like everything else - so a migration converts
their file toward **their** shape, and a shipped release never drags an overlaid file to a shape the
user did not choose.

**One step per release, applied in order, never skipped.** A definition going from 1 to 3 runs its
`1 -> 2` then its `2 -> 3`. A step that jumps a release cannot be reasoned about and cannot be
re-run.

**Each step states what it does with a targeted file it cannot parse** - which is **leave it alone,
leave it unstamped, and name it in the report**, always. An unparseable file stays outstanding rather
than being marked done.

**A file that is rebuilt rather than accumulated can declare a rebuild as its migration.**
`overview.md` is assembled from sources every time `/nk:project <name>` runs, so converting it field
by field is work nobody needs: the step is *rebuild it*. `/nk:upgrade` performs the rebuild exactly
as `/nk:project <name>` would, and the rebuild writes the new shape and its stamp like any other
write - which is also why `/nk:project` may rebuild an outstanding one itself (the schema-version
rule, *Only `/nk:upgrade` migrates*).

```markdown
## Migration
**1 -> 2 - rebuild.** Every field here is assembled from sources, so `/nk:project <name>` produces
the new shape directly; there is nothing to convert in place.
```

**A register or a ledger can never declare this**, because its entries exist nowhere else. The test
is whether the file's content is recoverable from something other than itself.

**A step is idempotent**, and the stamp is what makes it so: a file already at its definition's
version is skipped without being read.

**A conversion changes what its step names and nothing else, and that is checked before the stamp
goes on, in the same write.** Hold what you read, and confirm that every region the step did not name
came through unchanged - the append-only regions above all, which is where a placeholder written over
real content looks most like ordinary output. **If anything else moved, do not write and do not
stamp**: leave the file as it was and name it, exactly as for one that cannot be parsed.

**The stamp is why this is the one error with no second chance: interruption is recoverable and a
bad conversion is not.** A file converted **wrongly** is stamped exactly like one converted
correctly, so every later run skips it without reading it and no migration will ever look at it
again - which is why the check belongs before the stamp, in the same write, never in a pass
afterwards.

**`schema_version` is written last, once no definition has an outstanding file.** An interrupted
migration then reads as *store still at the old release, some files stamped at the new one* - which
is exactly the state the next section resolves.

**A migration removes only a field its step names**, and removes nothing else. **Never delete an
entry, a file or a directory** - the store's own rule holds here more than anywhere, because a
migration is the one kind of write that touches content the user did not just write.

**The user's overlay is never rewritten by a migration**, exactly as a plugin update never touches
`<store>/schema/` - the file-definition rule states it and what
to report where a moved shipped definition leaves an overlay behind.

## Finding what is outstanding, without reading the store

**This is the procedure, and every part of it is a glob or a grep.** No file is opened until it is
about to be converted.

1. **Resolve every definition** per the file-definition rule, and take its `schema:`. **A definition whose
   `schema:` is at or below the store's `schema_version` is finished before you start** - skip it without globbing.
2. **Glob that definition's files** inside the resolved store, and **count what came back.** That
   count is carried through the rest of the survey and into the report. **Shape the pattern as
   `<store>/**/<filename>`** and keep the hits at the definition's scope - never `projects/*/<filename>`,
   which can return zero with the files present (*A pattern that matches nothing*, in
   the store walk rules).
3. **Grep each for the stamp** - **anchored at the start of the line and never at the end.** A file is
   never read whole to find out whether it needs work.
4. **Stamp below the definition's `schema:` is outstanding. At or above is done.**
5. **Read and rewrite only the outstanding files**, stamping each in the same write.

**Every file the glob returned is accounted for as outstanding, done, or skipped, and the three add
up to the count from step 2.** Print the arithmetic per definition -
per the report shape - and never adjust it by hand.

**A definition that globs zero files is a finding, not a definition that is finished.** *Nothing
matched* and *nothing is outstanding* are the same output and mean opposite things: one is a store
with nothing to do, the other is a survey that did not look where the files are. **Say which one it
is** - name the pattern and the path the glob ran against, and where the definition's files would
live if there were any. **Before reporting a zero, re-run the glob in the other form** that
the store walk rules name; a zero only one form produced is the pattern's, not the store's. A
definition whose files exist and whose glob returned none must not be
counted as done, and **`schema_version` must not move on a survey that reported zero for a definition
without saying so.**

**The cost is the definitions that moved, not the size of the store.** A release that changes one
definition globs one definition's files and greps those; every other definition is answered at
step 1 without touching the disk.

**Progress is a derived count, not a record kept anywhere:** *overview.md, 240 of 310 converted*.
There is no progress file to go stale, for the same reason there is no `updated` field - **a record
nothing maintains is worse than no record**, and here the files already carry the truth.

**Resume is re-running.** Step 4 finds what the interrupted run left and nothing else. A migration
stopped at any point - a usage limit, a closed session, a crash - is finished by typing the command
again, and the second run costs only what the first did not reach.

**When a run ends without finishing, report what remains** by definition and by count, and say
plainly that re-running resumes. A user who does not know a migration is resumable will reach for a
manual fix, which is the one thing here that can actually lose content.
