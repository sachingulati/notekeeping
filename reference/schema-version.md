---
doc:   schema-version
title: The schema release, the version each definition carries, and how a store moves between them
---

# Schema versions

**The plugin ships one schema release, it is stated here, and it is stated nowhere else.** A command
that needs the number reads this file. A number typed into a command file drifts the first time the
schema moves, and a store written against a drifted number is one no version can read with
confidence.

| | |
|---|---|
| **The schema release this plugin ships** | **2** |

## What a schema release covers, and what it does not

**It versions the store's shape on disk** - which files exist, what frontmatter they carry, what the
resolver's columns are, and what a config file holds.

**It is not the plugin's version.** `plugin.json` carries that, and it moves on every release. Most
releases change prompts, wording and rules and leave every byte of an existing store valid; those do
not move this number. **A schema release moves only when a store that already exists would have to
change on disk to stay readable.**

Keeping the two apart is what makes the check below cheap enough to run everywhere: a user who
updates the plugin every week is told to migrate approximately never.

## Four places the number appears, in one numbering space

**There is one sequence of schema releases, and these four say different things about it.**

| Where | Says |
|---|---|
| **this file** | the release the plugin ships |
| **`schema:`** in a file definition | **the release at which this definition's shape last changed** |
| **the file stamp**, below | **the release whose shape this one written file was last written to match** |
| **`schema_version`** in a store's `config.md` | the release this store has been brought **fully** up to |

**A definition's number is what makes a migration targetable**, and it is the whole reason this is
per definition rather than one number for the schema:

```
a file is outstanding  <=>  its stamp  <  its own definition's `schema:`
```

**Bump one definition and only that definition's files are outstanding.** Raise
`project/overview.md` to `schema: 2` and every `overview.md` in the store needs converting; a
register whose definition still says `1`, holding files stamped `1`, is not touched and is never
read. **The target set is derived from the definitions, never declared in a list** - a list would be
a second statement of what the release changed, and two statements of one fact drift.

**A definition that never changes never moves.** Most sit at `1` permanently, which is what keeps
this cheap to maintain.

## The four answers, and there are no others

Every command that resolves a store may meet a store version that is not this file's release.

| The store's `schema_version` | Answer |
|---|---|
| **equal to this file's** | proceed |
| **older** | the store predates this plugin. **Say so and name `/nk:upgrade`, then carry on** - the per-file rule below decides what is actually blocked. Never migrate on the way past |
| **newer** | the plugin predates the store. **Refuse, and name the plugin update.** Never downgrade a store, and never guess what an unrecognised field means |
| **absent** | **stop and ask.** An absent version is never assumed to be the current one - a store written without one has no version, not this one |

**A store is never migrated by a command that was doing something else.** Exactly one command
migrates; every other command reports the gap and does not act on it.

### A release gap reaches a file, not the store

**A gap concerns only a file that is itself outstanding** - its stamp below its own definition's
`schema:`. If a release moved one definition, a `/nk:save` writing `session.md` is untouched by it:
`session.md`'s definition did not move, so nothing about that file is wrong. **Blocking the whole store
on a gap that reaches one file is the same error as reading everything to find out what moved.**

**Writing a file fresh is never affected**, at any gap - a new file is written to the current shape
and stamped at it.

What remains is the file that *is* outstanding and that a command is about to write. That is
`## Migration on write`, below.

## Migration on write

**A command that is already rewriting a file converts it in the same write**, where the step says it
may. The store then migrates itself as it is used, and `/nk:upgrade` is left with the files nobody
touched rather than being a prerequisite for getting any work done.

**The write was happening anyway, so the conversion is free** - no extra read, no extra write, and
nothing rewritten that the command was not already rewriting.

**Whether a step may ride along is the step's own declaration**, because only its author knows which
region of the file the change reaches:

```markdown
## Migration
**1 -> 2 - on write** - the handover block gains a `**Risk**` line. `/nk:save` rewrites that block at
every checkpoint, so the conversion is inside a write that was already happening.

**2 -> 3 - upgrade only** - every session block gains a heading level. Those are append-only history,
and rewriting them is not something an ordinary save may do.
```

**`shape:` cannot decide this and must not be used to.** `resume.md` is one file with both answers
in it: `## Where things stand` is rewritten at every save, and the story regions below it accumulate.
A ledger is the clearest case of all - **`decisions.md` is never edited by anything**, so no step on
it is ever `on write`.

| The step says | A command about to write the file |
|---|---|
| **on write** | **converts it, stamps it, and writes what it came to write** - one write, and **the report names the conversion**. No confirmation: the change is inside what it was already rewriting |
| **upgrade only** | **does not convert, and does not write that file.** Report it as outstanding and name `/nk:upgrade` |

**A chain rides along only if every step in it does.** A file two releases behind whose `1 -> 2` is
*on write* and whose `2 -> 3` is *upgrade only* is left to `/nk:upgrade` whole - a half-applied chain
is a file at a shape no release describes.

**A command never walks the store, never converts a file it was not already writing, and never moves
`schema_version`.** That is the invariant *never migrate as a side effect* was protecting, and it is
unchanged: what a command may do is finish the file in its hands, not go looking.

## The file stamp

```
<!-- nk: schema 2 -->
```

**One line, about twenty bytes, as the file's first line** - or directly below the frontmatter where
the file has some, which today is `requirements.md` alone. **One form everywhere**, including in
files that already carry an `authority:` or `verified:` comment, because the point of the stamp is
that a single anchored grep finds it in any file in the store.

**Anchor the search at the start of that line and never at the end**, and match the version as a
number rather than matching the whole line. **A line ending is not part of the stamp**: a file whose
lines end `\r\n` - or whose lines mostly do not, because only the lines something rewrote have
changed - carries a byte between `>` and the end of the line, and a pattern anchored with `$` matches
none of it. It is the writes **this plugin makes** that produce that state, so the stamp a migration
just wrote is exactly the one an end-anchored search cannot find again, and the failure appears only
on the second run. **Never rewrite a file's line endings to make a search work**; widen the search.

**An absent stamp is release 1.** Release 1 is the first shipped shape and nothing precedes it, so
there is nothing to backfill: **a release-1 store needs no stamps written into it today**, and a file
gains one from the first migration that touches it. That is why this costs nothing now.

**The stamp is written in the same write as the change it records - never in a second pass.** A file
is read, converted and stamped in one write, so a file is either unconverted and unstamped, or
converted and stamped. There is no third state, and that is what makes an interrupted migration
resumable rather than ambiguous.

**A stamp above its definition's `schema:` excludes that file from migration**, because the test
above is the only test. That is correct when the plugin is simply older than the store - and it is a
hazard when a stamp was raised by hand, which silently freezes one file at a shape nothing will ever
convert. **`/nk:doctor` reports it and never repairs it**: lowering a stamp schedules a rewrite of
the user's own file, which is not a repair anyone can make on their behalf.

**What carries no stamp, because it is never migrated:**

| | Why |
|---|---|
| **every projection** | derived. `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - losing every projection loses nothing, and `/nk:doctor --fix` rebuilds them |
| **`index.md`** | regenerated in full from `requirements.md` frontmatter, which is the thing that gets migrated |
| **`config.md`** | it carries `schema_version` itself |

**Regenerate these after a migration; never convert them.** A derived file that is migrated rather
than rebuilt can disagree with the source it is derived from.

## The history

| Release | What changed | Definitions it moved |
|---|---|---|
| **1** | the initial shape | all of them, at birth - every definition ships at `schema: 1` |
| **2** | **every block and every field carries a marker that names it** - so a reader and a `grep` find the same thing. `work/dev.md`'s blocks gain their kind (`## session <date>`, `## closed <date>`, `## open <date>`) -
**that half is now part of release 1**, `dev.md` having been split into `session.md` and `resume.md`
before any store existed, and both ship with the markers already; `overview.md`'s fields gain the bold label the definition never specified | **one, today** - `project/overview.md`. Every register and every ledger is untouched, because an entry's file and path already name it |

**Two shape changes predate release 1 and need no migration**, because no store was ever written with
either: `updated` was removed from `requirements.md`'s frontmatter, recency being derived from
`session.md`'s newest dated block, and `series` became `tags`. **A third joins them: `dev.md` was
split into `resume.md` and `session.md`** - current state and the record having proved to be two
files' work, not two regions of one. **Every store any shipped plugin has ever created is release
1**, which is why none of the three needs a migration: `/nk:adopt` is what meets an existing store,
and it reads rather than converts.

## Shipping a schema change

**Four steps, in this order**, and the last is what makes the first three checkable:

1. **Change the definition's shape**, and **raise its `schema:`** to the new release. Only the
   definitions whose shape actually changed.
2. **Add the `## Migration` step** to each of those definitions.
3. **Raise the release at the top of this file**, and add its row to the history.
4. **Check that they agree.**

**The release this file states is the highest `schema:` across every shipped definition.** That is an
invariant, not a convention - it is derived from the tree in one pass, and it catches the two
mistakes that are otherwise invisible until a user's store is wrong: a definition bumped with the
release left behind, and a release bumped with no definition moved. **It belongs in the release
checks**, where it costs nothing to run.

## Writing a migration

**Raise `schema:` on the definitions whose shape changed, and only those.** That bump *is* the
declaration of what the release touches; nothing else records it.

**The conversion lives with the definition, in a `## Migration` section**, keyed by the step it
performs:

```markdown
## Migration
**1 -> 2 - upgrade only** - `sources:` becomes a list. A header carrying one source string becomes a single-item
list. A header with no `sources:` at all is left alone and named in the report.
```

**A definition whose `schema:` is above 1 must carry a `## Migration` section covering every step up
to it.** Without one, a file is known to be outstanding and there is no way to convert it.
`/nk:doctor` reports a missing one as an error, exactly as it does a missing `## Exclusion`.

**Keeping the conversion beside the shape is what makes the overlay work.** A user's overlaid
definition carries its own `schema:` and its own `## Migration`, resolved through
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` like everything else - so a migration converts
their file toward **their** shape, and a shipped release never drags an overlaid file to a shape the
user did not choose.

**One step per release, applied in order, never skipped.** A definition going from 1 to 3 runs its
`1 -> 2` then its `2 -> 3`. A step that jumps a release cannot be reasoned about and cannot be
re-run.

**Each step declares `on write` or `upgrade only`** - `## Migration on write` above. A step that
declares neither is unusable: a command holding the file cannot tell whether finishing it is within
its remit.

**Each step states what it does with a targeted file it cannot parse** - which is **leave it alone,
leave it unstamped, and name it in the report**, always. An unparseable file stays outstanding rather
than being marked done.

**A file that is rebuilt rather than accumulated can declare a rebuild as its migration.**
`overview.md` is assembled from sources every time `/nk:project <name>` runs, so converting it field
by field is work nobody needs: the step is *rebuild it*, and the rebuild writes the new shape and its
stamp like any other write.

```markdown
## Migration
**1 -> 2 - on write** - rebuild. Every field here is assembled from sources, so `/nk:project <name>` produces
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

**The stamp is why this is the one error with no second chance.** *Stamp in the same write* makes an
interrupted migration safe - no file is half-done - but it applies just as faithfully to a file
converted **wrongly**: that file is stamped, so every later run skips it without reading it, and no
migration will ever look at it again. **Interruption is recoverable and a bad conversion is not**, so
the check belongs before the stamp rather than in a pass afterwards, which would have nothing left to
compare against.

**`schema_version` is written last, once no definition has an outstanding file.** An interrupted
migration then reads as *store still at the old release, some files stamped at the new one* - which
is exactly the state the next section resolves. This is `projections.md`'s *store first, projections
last* applied to the one operation that rewrites what is already there.

**A migration removes only a field its step names**, and removes nothing else. **Never delete an
entry, a file or a directory** - the store's own rule holds here more than anywhere, because this is
the one command that touches content the user did not just write.

**The user's overlay is never rewritten.** A plugin update does not touch `<store>/schema/` and
neither does a migration. Where a shipped definition moves and the user has overlaid it, **report
that their definition is now behind and what it would have to change**, then leave it exactly as it
is. An overlay silently "corrected" is the user's own decision overwritten.

## Finding what is outstanding, without reading the store

**This is the procedure, and every part of it is a glob or a grep.** No file is opened until it is
about to be converted.

1. **Resolve every definition** per `resolution.md`, and take its `schema:`. **A definition whose
   `schema:` is at or below the store's `schema_version` is finished before you start** - skip it without globbing.
2. **Glob that definition's files** inside the resolved store, and **count what came back.** That
   count is carried through the rest of the survey and into the report. **Shape the pattern as
   `<store>/**/<filename>`** and keep the hits at the definition's scope - never `projects/*/<filename>`,
   which can return zero with the files present (*A pattern that matches nothing*, in
   `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`).
3. **Grep each for the stamp** - **anchored at the start of the line and never at the end.** A file is
   never read whole to find out whether it needs work.
4. **Stamp below the definition's `schema:` is outstanding. At or above is done.**
5. **Read and rewrite only the outstanding files**, stamping each in the same write.

**Every file the glob returned is accounted for as outstanding, done, or skipped, and the three add
up to the count from step 2.** Print the arithmetic per definition -
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md` - and never adjust it by hand.

**A definition that globs zero files is a finding, not a definition that is finished.** *Nothing
matched* and *nothing is outstanding* are the same output and mean opposite things: one is a store
with nothing to do, the other is a survey that did not look where the files are. **Say which one it
is** - name the pattern and the path the glob ran against, and where the definition's files would
live if there were any. **Before reporting a zero, re-run the glob in the other form** that
`store-boundary.md` names; a zero only one form produced is the pattern's, not the store's. A
definition whose files exist and whose glob returned none must not be
counted as done, and **`schema_version` must not move on a survey that reported zero for a definition
without saying so.**

**This is the silent failure `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md` names for resolving a
store, in the one procedure that decides whether a store's content gets converted at all.** There, a
walk built on a listing reports *no store* while standing next to one. Here, a glob that matched
nothing reports *nothing to do* while standing on a store that needs every file converted - and
because step 4 treats at-or-above as done, **nothing revisits those files afterwards.** The count is
what tells the two apart, so derive it and state it rather than trusting the match.

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
