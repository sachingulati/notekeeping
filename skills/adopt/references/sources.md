# Adopt - the limit, the inventory, the context files, and what they point at

**Read this before phase 1.** How much one run reads and in what order, the inventory and the
narrowing, the context files and how far each one's entries may go, and following what they point at.

## The limit

**Adopt is iterative: one run reads at most `adopt_max_bytes`, and the next run carries on.** 5 MB
unless the store's config sets it (the config defaults). A run
reads in a fixed order - the context files, then the memory stores, then the pending set the newest
adopt run's `resume.md` carries, oldest first, then each new wave - **sizing each file before it reads
it, and taking whole files only while the total stays within the limit**. **The limit holds for
everything in the inventory alike** - a context file, a memory item, a followed file. Everything it
found and did not take is recorded `pending` in `inventory.md`, with what pointed at it and its size,
and the next `/nk:adopt` - a new run that continues this one - starts there.

**Reaching the limit stops the reading, not the run.** What was read goes through every phase - it
is classified, leaned, reported and, on a yes, applied and trimmed - and the report's last line says
what is left, in files and bytes, and to run `/nk:adopt` again to continue. **Say so in every run's first message and in its report**, with what is
left: one large run only grows the context, which costs tokens and lowers the quality of every
judgement in it; several runs of a bounded size cost less and place better.

**Sizes come from the file-facts rule's *size*, in batches, before anything is read.** **A file is
read whole or not at all**, and **a single file larger than the limit is not read** - the report
names it with its size, since no run would take it; raising `adopt_max_bytes` for one run is the
user's call. Where the size cannot be had, the run counts as it reads instead, per that rule's
fallback, and can pass the limit by one file. **A `<path>` inside a `.notekeeping` directory is refused by
name** - this store, `~/.notekeeping`, or any other: a store is never adopted from.

## The inventory

Phase 1 is cheap and phase 2 is not. Report the inventory first - how many files, what formats,
how much text - and let the user narrow before any content is read, with one `AskUserQuestion`.

**What was adopted before is not read again unless it changed.** Each adopt run's `inventory.md`
records every source it read, with its size and hash, every one it left `pending` and every root the
user `declined`. Phase 1 finds a source's newest row across the adopt runs - `Grep` on `^\| <path>
\|`, per that file's definition - and hashes the source (the file-facts rule's *content hash*): **a
changed hash is read again**, and an unchanged one is dropped, **unless the run that read it still
holds an open item from it** - a run never applied leaves its items open, so its sources are read
again, or that run is applied with `--apply`. Say how many of each. `--again` reads the unchanged ones too, for a run whose rules have
changed. Already-there holds on any re-read, so nothing is written twice. A declined root is not offered again
unless it is named as `<path>`.

**The inventory names the workspaces and projects this store registers**, and says that an entry
for one not registered stays where it is, untrimmed: if any are missing, stop here and register
them with `/nk:init` first, so this run knows where to place what it finds.

**The narrowing question is asked whenever the inventory holds more than one folder** - by root
and by folder within each root, with sizes. Dropping a folder is how scope is cut; what is
kept and not reached this run is `pending`, not lost. Where the pile is large, offer a sample in the
same question - *read all of it, or classify ~30 files first to see whether the targets fit?* - and
on that pick classify a stratified sample spread across the directories and formats the inventory
found. **Say it was a sample, and never present a sampled plan as a complete one.**

With no turn to answer either question, take the default: every folder found stands, none is
dropped, and no sample is taken - phase 2 classifies everything the inventory found. The report's
header records both answers - or that the defaults were taken.

**Above `one_pass_max_bytes` of inventory, phase 2 is drafted** - batches of files to subagents,
never more at once than `max_parallel_subagents`, each leaving its drafts, and phases 3 and 4 read the
drafts and never the sources, per the run engine's *The pass, and drafts*. A draft's target is the
store file and scope the classification gave it; phase 3's lean pass judges the drafts as a whole,
exactly as it would the entries.

## Context files - the other source, and where the trim reaches

Read every `CLAUDE.md` and `CLAUDE.local.md` the session loads - each ancestor of the working
directory up to the filesystem root, and your own `~/.claude/CLAUDE.md` - as sources like any other
pile, never a nested file found by scanning a subdirectory. Find the ancestors' by reading
`<dir>/CLAUDE.md` and `<dir>/CLAUDE.local.md` at every level, the working directory to the filesystem
root - all of them, past your home directory too; a missing file is an immediate answer, and
never `Glob` from the filesystem root. They are usually where knowledge went
when there was nowhere better to put it.

**A line that points into a `.notekeeping` directory is this plugin's own - the read line.** It is
never imported and never trimmed, wherever it sits; name in the report any that point at a store
other than this one.

**A source's reach caps where its entries go.** A source reaches as far as the sessions that load
it. `~/.claude/CLAUDE.md` loads in every session, so its entries may go anywhere, global included. A
`CLAUDE.md` or `CLAUDE.local.md` in a directory loads only under that directory. A file nothing
loads - a followed notes pile, a `<path>`, a README, a file a context file only points at - reaches
its own directory and no further. **Being pointed at is not being loaded**: the pointer line in a global
context file is itself the global entry, and the file it names is read at its own reach. An entry
goes no wider than the store holding its source - global only from a source every session loads,
otherwise the project or workspace whose directories contain the source, or whose every session
loads it, as an ancestor's `CLAUDE.md` above the workspace root. **A source no store holds stays
where it is**, reported as unheld, exactly as *An entry about a project this store does not hold*
below: its facts may be about the machine, but they reached only the sessions that opened that file,
and global would put them in front of every session.

Within that cap, every entry is placed by what it is about, never by the file it came from. No
source maps to a target: `~/.claude/CLAUDE.md` mostly lands in global, but a line about one project
goes to that project, and a file above the workspace root is sorted the same way. What is needed in
every session goes to that scope's `NOTES.md`; the machine itself - installed tools, paths,
versions, scripts, layout - goes to global's `environment.md` when its source reaches global; what
is needed only sometimes goes to the register it belongs to - `process.md` for how work moves, and
`gotchas.md`, `decisions.md`, `domain.md` for the rest - where it costs nothing until read; an
instruction goes to the `instructions.md` of the scope it holds at. Do not skip global because a
source file is large: it is where the always-needed half comes back.

**An entry about a project this store does not hold stays where it is, untrimmed.** Say where it
belongs - a workspace in global's `## Workspaces` and that store's `## Projects`, probed before it is
named per the resolution cases, *Knowing that other stores exist*, or *no store holds it* - and suggest
the next step: `/nk:init` to register the workspace or the project if it is not, then `/nk:adopt`
there. **A suggestion only**: this command never writes outside this store, and nothing but `/nk:init`
creates a project. An entry that names no project is placed by content as any other.

One source of truth is the point. A fact copied into the store and left in a context file is now
in two places, drifting, and paying always-loaded cost in one of them. So the move is finished
rather than half-made.

### Follow what they point at, run by run

A context file points at a notes repo; that repo's index points at three directories; one of those
points at a runbook somewhere else again. All of it is the material - **reached over as many runs as
it takes**, never in one. References are followed from anything read, not only from context files.

1. **An explicit reference, never an inference.** A path, directory or repository written in the
   text. Not a directory that looked promising, and not a sibling you noticed on the way past.
   **Never one that leads into a `.notekeeping` directory** - not followed, and named.
2. **Keep a visited set**, keyed by resolved absolute path - this run's reads and the earlier adopt
   runs' `inventory.md` rows together - and read nothing twice. That is what makes a cycle safe.
3. **Consent is per root, not per file.** `<path>` and the context files are the first roots.
   Anything resolving inside an approved root is read freely. A reference resolving outside every
   approved root is a new root: collect the wave, report each with what pointed at it, and let the
   user drop any before you read them - one `AskUserQuestion` per wave, and only that. A dropped root
   is recorded `declined`. With no turn to answer, nothing in the wave is dropped. A typed answer
   re-enters and the walk starts again from phase 1 with it applied; say so.
4. **Repeat until the frontier is empty or the limit is reached**, whichever is first. What is left
   is `pending` for the next run. There is no depth cap: the limit bounds a run, not the material.
5. **What cannot be read is named, never guessed.** A URL, a wiki link, a path that does not exist, a
   repository you cannot reach, a read refused: report it under unfollowed references, with what
   pointed at it. This command has no network access and must not imply otherwise.

**A followed pile, like a `<path>`, is a source and never a target.** The trim (`trim.md`) reaches context
files and this session's own memory store only - nothing inside one is edited, moved or removed, however much is copied out of it.
