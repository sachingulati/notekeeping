---
description: Move a store to the schema version this plugin ships. Converts only what is outstanding, and resumes.
argument-hint: "[--apply [<report>] [all | <numbers>]] [--dry-run] [--page | --no-page] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git rev-parse:*), Bash(git remote:*), Bash(git log:*), Bash(git -C:*), Artifact
---

Move a store from an older schema version to the one this plugin ships. **Nothing is written without
a yes, or `--apply` on a saved report.** Writes inside the store, plus the projections step 11 regenerates.

**The bare command surveys, proposes the work with its counts, saves the proposal as a report, and
asks once.** A yes migrates exactly what was proposed, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s `## A yes applies what was shown`; with no turn
to answer in, it writes nothing - not a stamp, not `schema_version`. **`--dry-run` proposes and does
not ask.** **`--apply [<report>] [all | <numbers>]` applies a saved report** - the latest when none is
named - per that file's *The report is saved*. **Each item is one definition that moved**, so `all`
is the whole migration and numbers are part of it; a partial migration is safe, because re-running is
already how a migration resumes. **An item is stale when its definition's version, or its count of
outstanding files, has moved since the report** - skip it and say so.

**`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

**Resolve the store first**, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. The version
this plugin ships, the four answers, the file stamp and the rules a migration follows are
`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` - **read the number there and never state one
from here.**

## Two stores, two versions

**Global and the workspace store are separate stores and carry separate versions.** Both resolve from
where you are standing, so **check both and report both**, and migrate each on its own answer.

**Then name the machine's other workspaces.** Global's `## Workspaces` registry lists them -
`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. For each one that is not the store you resolved:
**probe that its store is actually there**, read its `schema_version`, and report where it stands.

**Offer to migrate them, one confirmation each, by absolute path.** A store you are not standing in
is a bigger action than the one you are, so it is never folded into the first confirmation and never
assumed. **Decline is a normal answer** - each is reachable by running this command from inside it.

**A path that does not probe is reported, not repaired.** It may be an unmounted volume. Say which
entries could not be reached and move on.

## What it does

**Under `--apply`, steps 1-8 do not run: the saved report is the proposal.** Check each chosen item
for staleness, then go to step 10 with those items.

1. **Read `schema_version` from each resolved store's `config.md`**, and compare per
   `schema-version.md`'s table.
2. **Equal, in both?** Report `no-change` and write nothing. This is the ordinary outcome and it
   should cost nothing to ask for.
3. **Newer than the plugin?** **Refuse**, and name the plugin update. Never downgrade, and never
   rewrite a field you do not recognise.
4. **Absent?** **Stop and ask** which version the store was written at. Do not assume it is the
   current one, and do not assume it is the oldest.
5. **Older?** **Resolve every definition and read its `schema:`.** The definitions that moved are
   the only ones with anything to do; a definition still at the store's release is finished before
   you start, and is never globbed.
6. **Survey those definitions without reading the store**, per `schema-version.md`'s *finding what
   is outstanding*: glob each moved definition's files, grep each file for the stamp, and count how
   many sit below the definition's version. **A glob and a grep per file and nothing more** - so the
   survey costs the definitions that moved, not the size of the store. **Carry the number of files
   the glob returned**, and treat a definition that returned none as something to report rather than
   as one with nothing to do - `schema-version.md` says which is which, and why the two look
   identical.
7. **Propose the work with those counts**, before writing anything: each definition that moved, its
   step or steps, how many files are outstanding and how many are already done.
8. **Save the proposal as a report, then ask once whether to migrate it.** Under `--dry-run` save
   nothing and stop; with no turn to answer in, stop here - the saved report is the output.
9. **A yes - or `--apply` on the saved report - is the go-ahead for the migration as proposed**; work
   the steps in order. It is
   answered after the counts are on the screen, which is what makes it an approval of them.
10. **Convert the outstanding files one at a time**, following the `## Migration` section of the
    **resolved** definition - the user's overlay wins here as everywhere, so a migration converts
    toward the shape that store actually uses. Read each file only when it is about to be rewritten,
    and **stamp it in the same write as the change**. **Before that write, confirm that everything
    the step did not name survived it** - `schema-version.md` says what to do when something else
    moved, and why a wrongly converted file is the one error a later run cannot find.
11. **Regenerate what is derived rather than migrating it** - `index.md` per
    `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md`, then every projection of the store migrated
    per `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - `~/CLAUDE.local.md` when that store is
    global. **Store first, derived last.**
12. **Write `schema_version` last**, once no definition has an outstanding file.
13. **Report**, per below.

**Re-run the survey after the confirmation and before the first write, and compare it against the
one you proposed.** It is two globs and a grep per file, it costs almost nothing, and it is the only
thing standing between a survey that was wrong and a store rewritten on its numbers. **Where the two
disagree, write nothing**: say which definition moved, give both counts, and ask again. A
confirmation answers the counts that were on the screen, so counts that have changed are not
confirmed - and the likeliest reason they changed is that one of the two surveys is wrong rather than
that the store moved underneath you.

**The unit of consent is the migration, not the file.** A per-file prompt across a mature store is a
question nobody finishes, and a half-answered migration is the state this command exists to avoid.

**Expect the outstanding count to be lower than the file count, and say so.** Where a step is *on
write*, ordinary work has already converted the files the user has touched since the release - so
this command is the sweep for what nobody opened, not the thing standing between them and their
notes.

**Never hold the store in context to migrate it.** A file is read, converted, written and let go. The
survey in step 6 is what tells you the size of the job, and the stamps are what carry the progress -
so nothing has to be remembered between one file and the next.

## Stopping part-way is a supported outcome

A long migration can end before it finishes: a usage limit, a closed session, an interruption. **That
is safe, and it is the reason for the stamp.**

- **Every file is converted and stamped in one write**, so there is no file that is half-done.
- **`schema_version` moves only at the very end**, so a store that was interrupted still reads as the
  old release - which is true, because some of it is.
- **Re-running is the resume.** The survey in steps 5 and 6 finds exactly what is left, and the
  second run costs only what the first did not reach. **Nothing is converted twice**, because a file
  at or above its definition's version is skipped without being read.

**Say this in the report whenever a run ends with anything outstanding**, with the counts. A user who
does not know a migration is resumable will reach for a manual fix, and a hand-edited store part-way
through a migration is the one thing here that can genuinely lose content.

## What it never touches

**The user's overlay at `<store>/schema/`.** A plugin update does not touch it and neither does this.
Where a shipped definition moved and the user has overlaid it, **name the overlay file, say that it
is now behind and what it would have to change**, then leave it byte-identical. An overlay quietly
corrected is the user's own decision overwritten. **Their overlay still governs their files**: the
conversion follows the resolved definition, so an overlaid file is never dragged to a shape they did
not choose.

**A fragment - an overlay carrying `extends:` - inherits the move.** What it does not name comes
from the shipped definition, so a step touching an inherited part applies normally and needs no
mention. **Where a step moves the exact part the fragment overrides, the fragment still wins**:
convert toward the resolved shape, then **name the fragment, the step, and which part of the new
shipped shape it is holding back**. That sentence is the whole remedy - the user decides whether to
change their fragment, and this command never does.
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`.

**Anything it does not recognise.** A file, a field or a directory no step names is left exactly as
it is **and named in the report**. A migration that tidies on the way past is one nobody can predict.

**A targeted file it cannot parse.** Leave it, **leave it unstamped**, and name it. An unparseable
file stays outstanding rather than being marked done - which means a later run, or a human, can still
finish it.

**Anything outside the store other than the projections step 11 regenerates**, and nothing is ever
deleted - not an entry, not a file, not a directory. A field is removed only where a step names that
field.

## `--dry-run`

Print the chain and, for each step, its targets and the outstanding count from the survey. Name the
overlay files affected. **Write nothing**, including stamps and `schema_version`, and **do not ask** -
this is the bare command's proposal without its question.


**The report can be a page.** Where there is enough to choose between, offer it at the very
end, per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md` - which owns the offer, the two
flags, and what the page may carry. **The terminal report is printed either way.**

## Report

1. **Each store, its version before and after**, by absolute path - the resolved workspace store and
   global, said separately.
2. **Each definition that moved: files found, then converted, already done, outstanding and skipped
   - and the four accounting for the files found.** Counts derived from the survey and from what you
   actually wrote, never estimated, and the arithmetic printed rather than asserted. **A file that is
   none of the four is a category this report does not have**, and a frozen stamp is `skipped`, not
   something named in prose beside a table that does not add up. **Say which definitions were skipped
   entirely** because their version did not move - that is the bulk of the store, and it is the
   evidence that the run cost what it should have. **Derive that number too, by counting the
   definitions you actually resolved at step 5** - not from a total carried in your head, and not
   from one stated anywhere else. A total recalled rather than counted is the error this whole report
   is shaped to prevent, and it is the one that looks most like arithmetic when it is wrong.
3. **Whether the chain completed.** If anything is outstanding, say so, say re-running resumes, and
   do not report the store at the new version.
4. **What was left alone** - overlay files affected, files that could not be parsed, anything
   unrecognised, and why. **Never empty by omission**; if there is nothing, say so.
5. **Every count follows `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`** - and the
   converted count comes from the files you actually wrote, never from the survey that proposed
   them. The gap between proposed and done is what the number exists to expose.

## Never

- **Never skip a version**, and never migrate a store this command neither resolved nor probed from the
  `## Workspaces` registry and had confirmed by its absolute path.
- **Never downgrade**, and never write into a store whose version is newer than the plugin's.
- **Never walk the store on another command's behalf.** An ordinary command may finish the one file
  it is already writing, where the step says *on write* - `schema-version.md`'s
  `## Migration on write`. **Only this command surveys, converts files it was not asked to write, or
  moves `schema_version`.**
- **Never stamp a file you did not convert**, and never stamp in a pass of its own. A stamp that does
  not travel with its change is a claim that a file was migrated when it was not.
- **Never lower a stamp**, and never convert a file stamped at or above its definition's version. A
  stamp raised by hand freezes that file out of migration - that is `/nk:doctor`'s finding to report,
  not this command's to overrule.
- **Never move `schema_version` while any definition has an outstanding file.**
- **Never rewrite the overlay**, and never delete anything a step did not name.
- Never write outside the store, other than the projections of step 11.
