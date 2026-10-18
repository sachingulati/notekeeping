---
doc:   upgrade-migrate
title: Migrating a store that is behind - the survey, the proposal, the conversion and its report
---

# Migrating a store

**Read this when a store is older than the plugin, or the run is `--apply`, *Apply* or a re-entry.**
A store at the plugin's version has nothing here to do.

Under `--apply`, *Apply* and a re-entry, steps 5-8 do not run: the run's report is the proposal.
*Apply* and `--apply` check each chosen item for staleness, then go to step 10 with those items.

5. Resolve every definition and read its `schema:`. The definitions that moved are
   the only ones with anything to do; a definition still at the store's release is finished before
   you start, and is never globbed. **A step marked *gather*** takes its sources from its step, not
   from the definition's own files - `migration.md`, *Writing a migration*.
6. **Survey those definitions without reading the store**, per `migration.md`'s *finding what
   is outstanding*: glob each moved definition's files, grep each file for the stamp, and count how
   many sit below the definition's version. A glob and a grep per file and nothing more - so the
   survey costs the definitions that moved, not the size of the store. Carry the number of files
   the glob returned, and treat a definition that returned none as something to report rather than
   as one with nothing to do - `migration.md` says which is which, and why the two look
   identical.
7. **Propose the work with those counts**, before writing anything: each definition that moved, its
   step or steps, how many files are outstanding and how many are already done. **An item is a
   definition with at least one file outstanding.** A moved definition with none - a gather that
   found no source, a step with nothing to convert - is counted in the arithmetic line, never
   numbered as an item.
8. **Write the proposal as the run's report, then ask the report question.** Under `--dry-run`
   mint nothing and stop; with no turn to answer in, stop here - the run is the output.
9. *Apply* - or `--apply` on the run - is the go-ahead for the migration as the report holds it,
   its items' answers included; work the steps in order, each logged per the report-apply rule. It is answered after the counts
   are on the screen, which is what makes it an approval of them.
10. **Convert the outstanding files one at a time**, following the `## Migration` section of the
    resolved definition - the user's overlay wins here as everywhere, so a migration converts
    toward the shape that store actually uses. Read each file only when it is about to be rewritten,
    and stamp it in the same write as the change. **Before that write, confirm that everything
    the step did not name survived it** - `migration.md` says what to do when something else
    moved, and why a wrongly converted file is the one error a later run cannot find. Where a step is
    a rebuild, this command performs it itself - a project's `overview.md` is rebuilt
    here per the overview rule (with
    the repo-facts rule), exactly as `/nk:project` would rebuild it, not
    deferred to that command.
11. **Regenerate what is derived rather than migrating it** - `index.md` per
    the index-shape rule and the index-writing rule. Store first, derived last.
12. **Write `schema_version` last**, once no definition has an outstanding file.
13. **Report**, per below, and finish the run - its states, `summary.md` and `resume.md`, per the
    run engine's phase 7.

**Re-run the survey after the go-ahead and before the first write, and compare it against the
report.** It is two globs and a grep per file, it costs almost nothing, and it is the only thing
standing between a survey that was wrong and a store rewritten on its numbers. An item whose count
disagrees is stale: skip it, say which definition moved, and give both counts - the report-apply rule's
per-item check, the same rule `--apply` follows. The items that agree go ahead. An approval answers
the counts that were on the screen, so counts that have changed are not approved - and the likeliest
reason they changed is that one of the two surveys is wrong rather than that the store moved
underneath you. A later bare run proposes the skipped ones again.

The unit of consent is the migration, not the file.

## Nothing outstanding

**A store behind the plugin whose survey finds no file outstanding** - every moved definition a
gather with no source or a step with nothing to convert - **is a clean run**: it mints nothing (the
run engine), writes `schema_version` at once, and its reply names the store, the release before and
after, and the definitions surveyed. Nothing is converted, so nothing waits on an answer. A
definition whose glob returned zero where its files would live is a finding, not nothing
outstanding: it is reported, and `schema_version` stays where it is. `--dry-run` writes nothing.

**Never hold the store in context to migrate it.** A file is read, converted, written and let go. The
survey in step 6 is what tells you the size of the job, and the stamps are what carry the progress -
so nothing has to be remembered between one file and the next.

## Stopping part-way is a supported outcome

A long migration can end before it finishes: a usage limit, a closed session, an interruption. The
resume and stamping rules that make this safe are the migration rules
- re-running is the resume, and nothing is converted twice.

Say this in the report whenever a run ends with anything outstanding, with the counts. A user who
does not know a migration is resumable will reach for a manual fix, and a hand-edited store part-way
through a migration is the one thing here that can genuinely lose content.

## What it never touches

**The user's overlay at `<store>/schema/`, whole or a fragment carrying `extends:`.** A plugin
update does not touch it and neither does this; precedence and what a fragment inherits are
the overlay rules. Where a shipped definition moved and the
user has overlaid it, or a fragment holds back the exact part that moved, **name the file, say what
it is now behind, and leave it byte-identical**. The user decides whether to change it; this command
never does.

**Anything it does not recognise.** A file, a field or a directory no step names is left exactly as
it is and named in the report. A migration that tidies on the way past is one nobody can predict.

**A targeted file it cannot parse.** Leave it, **leave it unstamped**, and name it. An unparseable
file stays outstanding rather than being marked done - which means a later run, or a human, can still
finish it.

**Anything outside the store**, and nothing is ever
deleted - not an entry, not a file, not a directory. A field is removed only where a step names that
field, and a source only where a gather step converted it and its copy is in this run's `copies/`.

## `--dry-run`

Print the chain and, for each step, its targets and the outstanding count from the survey. Name the
overlay files affected. **Write nothing**, including stamps and `schema_version`, and **do not ask** -
this is the bare command's proposal without its question.

## Report - a run that migrated, or proposed to

1. **Each store, its version before and after**, by absolute path - every store surveyed, said
   separately.
2. **Each definition that moved: files found, then converted, already done, outstanding and skipped
   - and the four accounting for the files found.** Counts derived from the survey and from what you
   actually wrote, never estimated, and the arithmetic printed rather than asserted. A file that is
   none of the four is a category this report does not have, and a frozen stamp is `skipped`, not
   something named in prose beside a table that does not add up. Say which definitions were skipped
   entirely because their version did not move - that is the bulk of the store, and it is the
   evidence that the run cost what it should have. Derive that number too, by counting the
   definitions you actually resolved at step 5 - not from a total carried in your head, and not
   from one stated anywhere else.
3. **Whether the chain completed.** If anything is outstanding, say so, say re-running resumes, and
   do not report the store at the new version.
4. **What was left alone** - overlay files affected, files that could not be parsed, anything
   unrecognised, and why. Never empty by omission; if there is nothing, say so.
5. Every count follows the report-shape rule - and the
   converted count comes from the files you actually wrote, never from the survey that proposed
   them. The gap between proposed and done is what the number exists to expose.

The report can be a page - the report question's *Publish as a page*, before anything is converted, per
the report-pages rule, which owns the two flags and what the page may
carry. The terminal report is printed either way.
