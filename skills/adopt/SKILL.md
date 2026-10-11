---
name: adopt
description: Read the notes and context files the user already has - CLAUDE.md files, docs, memory - build the store from them, and trim what moved. Use when the user asks to import or adopt existing notes into Notekeeping.
argument-hint: "[path] [--again] [--apply [<report>] [all | <numbers> | <categories> | <text>]] [--dry-run] [--page | --no-page]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact, ArtifactData
---

Read what you already have - a notes folder, a wiki export, another tool's store, your `CLAUDE.md`
files - and build the store out of it. Every question this skill asks - the narrowing, a wave
of new roots, the report question, the walk - follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`. What it needs from a repository
it asks git, per `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`; a file's size and hash, and
the trim copy, are per `${CLAUDE_PLUGIN_ROOT}/reference/store/file-facts.md`.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs. **Then the schema release**: read each resolved store's `schema_version` and `Grep` the release in `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (*The schema release this plugin ships*). A store behind it is said in one line on every run, naming `/nk:upgrade`, and the run carries on; newer or absent follows that file's four answers.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:adopt`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

**A re-entry? Decide before any read.** Where the conversation holds this skill's **report question**
with an answer typed after it, and the report that question named has no `## Applied`, **the material
is not read again**: read that report, append the answer as `## Answers` keyed by item number, show
each item's resolved write, and ask the report question again - the report-apply rule's re-entry. Where it
holds the **view question** with an answer typed after it, read that report, carry out the pick and
ask the report question - the material is not read again either. Where it
holds the **narrowing** or **a wave's** question with a typed answer, the pass starts again from
phase 1 with that answer applied - the inventory is cheap - and the report's header says which folders
were dropped. Otherwise the run is fresh.

**No store means refuse and name `/nk:init`.** That command creates the store; this one fills it. Resolve every
file definition you write through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, so the
user's overlay wins here exactly as it does everywhere else.

## The two forms

| Form | Reads |
|---|---|
| `/nk:adopt` | your **native context** - every `CLAUDE.md` and `CLAUDE.local.md` the session loads, and **every harness memory store** (*Harness memory*, below) - then what they point at, wave by wave |
| `/nk:adopt <path>` | that path, then what it points at, the same way |

**The bare form runs first.** Native context is small, loaded in every session, and the only place a
trim can reach, so it is where adopting pays most and costs least. `<path>` is for a pile nothing
points at.

**Adopt is iterative: one run reads at most `adopt_max_bytes`, and the next run carries on.** 5 MB
unless the store's config sets it (`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`). A run
reads in a fixed order - the context files, then the memory stores, then what `adopted.md` holds as
`pending` from earlier runs, oldest first, then each new wave - **sizing each file before it reads
it, and taking whole files only while the total stays within the limit**. **The limit holds for
everything in the inventory alike** - a context file, a memory item, a followed file. Everything it
found and did not take is recorded `pending`, with what pointed at it and its size, and the next
`/nk:adopt` starts there.

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

**Never start from a directory nobody named.** No scanning for note-shaped folders, no adopting a
`notes/` because of what it is called - `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` forbids
inferring from the filesystem, and it holds here. Every root is either typed by the user or written
in something you read.

**Started by Claude rather than typed, a read outside the working directory asks for permission** -
the context files above it, `~/.claude/CLAUDE.md` - and so does git pointed at another folder. That
is expected: let each prompt come, and never work around one - no other path to the same file. A refused read is
a file this run did not read, named in the report.

## One confirmation

The unit of consent is the store, not the entry. Show what you are about to build - the targets,
their counts, what was excluded and why - then write the whole thing on one go-ahead.

What makes one confirmation safe is rule 3 below: everything written into the store is a copy.
The worst case is a store you delete and run again, with your own material untouched. **The one
exception is the trim**, which is why it is shown line by line before the question is asked, and why
every trimmed file is copied whole into the store's `tmp/` before it is touched.

## Nothing is written without a yes

The bare command reports the inventory and lets you narrow it (phase 1), then reads, classifies and
leans, and saves both halves - what would be written into the store, and what would be trimmed - as
a report, **grouped by category** (*Report*, below). The terminal shows the categories and their
counts. Then two questions, in this order, each with `AskUserQuestion`:

1. **The view question - *Publish as a page / Show here / Not now*.** This is how the items are read
   before anything is decided. *Publish as a page* publishes the saved report, per
   `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`; *Show here* prints every item, category by
   category. Where that rule would not offer a page - no publishing tool, `--no-page` - the pick is
   not offered; where a publish fails, print the items here and go on. `--page` publishes without
   asking. *Not now* ends the run with the report saved for a later `--apply`.
2. **The report question - *Apply / Apply and trim / Trim only / Not now*** - once the items have
   been shown, per `${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`'s `## A yes applies what was
   shown`:

   | Pick | Writes, exactly as the report holds it, its `## Answers` included |
   |---|---|
   | **Apply** | the store half; nothing is trimmed |
   | **Apply and trim** | both halves, the store first |
   | **Trim only** | the trim items whose content is already in the store, or that the lean pass dropped - an earlier apply's trim. One whose content is not in the store is skipped, and the report says so |
   | **Not now** | nothing; the report stays for a later `--apply` |

   **Where the page was published and keeps its selection**, *Apply the page's selection* takes the
   place of *Trim only* - the page's ticks name writes and trims alike - per the report-apply
   rule's pick of that name.

   An answer of your own - *only A and C*, *3: merge into auth* - is typed, and comes back as a
   re-entry.

With no turn to answer in, neither question is asked and nothing is written; the saved report is what
a later `--apply` applies.

`--dry-run` shows both halves, does not ask, and saves no report. Worth typing on a large or
unfamiliar pile, to see the plan before committing to it.

`--apply [<report>] [all | <numbers> | <categories> | <text>]` applies a saved report, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`'s *The report is saved* - the latest when none is
named; with no selection it walks the items, one `AskUserQuestion` each. It never reads your material
again: the report is what gets written.

The trim is items like any other. Its lines are in the report line by line, so `all` includes
them, numbers can name them, and text can narrow to or away from them - *"skip the trim"*. Every
trimmed line is one the report showed, and **the four rules below hold whatever the selection is**:
nothing is removed that did not reach the store first.

### `--apply <text>`

Free text selects and excludes, within the saved report. *"Only the gotchas"*, *"skip the
wiki export"* - it picks from the proposals the report already carries. It cannot supply a
judgement the classification was missing, since applying never reads the material again.

**It cannot reach past the report**, and **the four rules below
hold whatever it says.** Text asking for something outside them is refused by name, and the rest of
the instruction is still honoured. Say which writes the proposal earned on its own and which the
text authorised.

## The five starting states

| You have | This does |
|---|---|
| **Nothing** | Nothing to do - say so |
| **A pile of markdown** - a notes folder, a wiki export, a vault | The whole pass below |
| **`CLAUDE.md` files and little else** | Read them as sources, write what is knowledge, and offer to trim what moved |
| **Another tool's store** | Map its convention onto the schema where it is unambiguous, and name what is not |
| **An older version of this store** | A schema upgrade, not an adoption - **say so, name `/nk:upgrade`, and stop** |

## The pass

| Phase | Does | Writes |
|---|---|---|
| **1 · Inventory** | Walk what this form reads (above), less what `adopted.md` records as read and unchanged, or declined (below), plus what it holds as pending. Files, sizes, formats. Do not assume markdown | no |
| **2 · Classify** | Read content, and test every file and every section against each definition's admission and exclusion tests - never a `bookkeeping` one. **Read what the store already holds at every target** - a duplicate and a contradiction with the store are found here, not at the build | no |
| **3 · Lean** | Read the classified result **as a whole**, every target file together with what it already holds, and apply *Worth recording* (below) - drop, merge, move on-demand | no |
| **4 · Plan** | The store you are about to build: every target, its entry count, everything excluded and dropped - and the trim, listed separately, with each file's trimmability | no |
| **5 · Confirm** | The report saved, then the report question, covering the store, the drops and the trim as shown | the report only |
| **6 · Build** | Create the files and write the entries | **yes, in the store** |
| **7 · Trim** | Copy each file to be trimmed into `tmp/`, then remove from it only what reached the store or was dropped, and only what the yes covered | **yes, outside the store** |
| **8 · Record and report** | Append to `adopted.md` each source read, each left pending and each root declined; say what was written, dropped, removed and not, what is left, and what needs a human eye | **`adopted.md`** |

Phase 1 is cheap and phase 2 is not. Report the inventory first - how many files, what formats,
how much text - and let the user narrow before any content is read, with one `AskUserQuestion`.

**What was adopted before is not read again unless it changed.** The store's `adopted.md` records
every source a run read, with its size and hash, every one it left `pending` and every root the
user `declined` - written once the run applies, per its definition. Phase 1 hashes each recorded
source (the file-facts rule's *content hash*): **a changed hash is read again**, and an unchanged one
is dropped. Say how many of each. `--again` reads the unchanged ones too, for a run whose rules have
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

**Phase 2 may hand batches of files to subagents, never more at once than `max_parallel_subagents`**,
per the store-resolution rule.

## What it writes into the store

Registers and ledgers, and the two project documents - `gotchas.md`, `patterns.md`,
`decisions.md`, `domain.md`, `runbook.md`, `architecture.md`, `process.md`, global's
`environment.md`, each scope's `NOTES.md` and `instructions.md`, and a project's `overview.md` where
the material supports them.

Write only what the material earns. A target with no admitted entry is not created: an empty
register is a stub, and a stub looks answered. `/nk:init` follows the same rule, beyond the files a
store or project needs in order to exist.

Check the target before writing. An entry whose text already matches one already in the store is
skipped, not duplicated - a second `/nk:adopt` pass over the same material adds only what is new.

Every entry keeps the format its definition specifies, and gains no source or date. Where a
`verified:` stamp cannot be established from what you read it is `verified: unknown` - visible,
never guessed.
A write that leaves a file with a numeric `budget:` at or past `budget_notice_pct` of it says so, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`.

Then `index.md` per `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` and `${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md` if any work item was
written. This command writes no read line: registration does, and the notes it builds are what the lines point at.

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
named per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, *Knowing that other stores exist*, or *no store holds it* - and suggest
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
2. **Keep a visited set**, keyed by resolved absolute path - this run's reads and `adopted.md`'s rows
   together - and read nothing twice. That is what makes a cycle safe.
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

**A followed pile, like a `<path>`, is a source and never a target.** The trim below reaches context
files and this session's own memory store only - nothing inside one is edited, moved or removed, however much is copied out of it.

### What moves, and what stays

Routing a candidate - admission, scope, already there - is per `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`.

Both move, and they go to different files. A fact you *look up* - a port, a name, a trap, a
decision and its reason - goes to `NOTES.md` or the register it belongs to. A directive you *obey* -
house style, a workflow rule, *always run the linter* - goes to the `instructions.md` of the scope it
holds at: the project, the workspace, or global for one that holds everywhere. **Never merge the
two**: a fact filed as an instruction becomes unfalsifiable, and an instruction filed as a fact
becomes optional. The test is the schema's own
(`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/global/NOTES.md`).

**A line that holds both is split.** *`jq` is installed; use it for every JSON edit* is a fact
and a directive: the fact goes to its register, the directive to `instructions.md`, each in the source's
own words. **A split separates; it never rewords** - a reworded rule is a different rule, and nobody
approved it.

**An instruction is a directive the agent obeys, and nothing else is.** A fact about a tool, a
permission the user granted, a description of the file it came from, a choice and its reason - none
is an instruction, whatever file it was in or however it is phrased.

**An instruction that holds only when a task reaches it is not loaded always.** Five rules for
cutting a release apply only when releasing. Its detail goes to the on-demand register it belongs
to, and `instructions.md` keeps one trigger line - *before cutting a release, read `<file>`* - which the report shows beside the lines it replaces. **An instruction found under a
`<path>` was never in force**, since nothing loaded it: it is proposed, never applied under `all`,
and the report says that applying it changes how the agent behaves.

**The budget does not bind the run.** Every instruction from a context file is placed, and the lean
pass, not the ceiling, decides what earns a line. The write says so per the budget notice, above,
and the report names each file it left past its ceiling.

### The lean pass

**After classifying, read the result as a whole** - each target file as it would stand, its present
entries included, and every file beside it - and apply
`${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`, *Worth recording*, to every entry. Per entry it can
**keep** it, **drop** it, **merge** it with another that says the same, or **move** it to an
on-demand register with a trigger line. Each verdict that is not *keep* is in the report with its
reason, in a section of its own.

**Looking at the whole is the point.** Per-entry admission can pass every entry while the result
fails - a file past its budget, one fact in two files, five lines where one trigger line would do.
Only a pass over the whole file sees that.

**A dropped line is trimmed like a moved one** - shown, quoted, covered by the same yes, and copied
whole into `tmp/` first. Left in its context file it would go on loading in every session, which is
what dropping it was for.

### Every line, and the trim

**Every line of a context file gets a verdict - moved, dropped or kept - and a line leaves its file only
under the trim invariant.** When the inventory holds a context file, or a report being applied holds a
trim item, read `${CLAUDE_PLUGIN_ROOT}/skills/adopt/references/trim.md` before phase 2: the verdicts, the trim
invariant, which files can be trimmed, the trim copy, and how the trim is shown.

## Harness memory

**The bare form reads every harness memory store** as a source, within the limit, places its items by
the drain's rules, and clears only this session's own directory's store. When the inventory holds a
memory store, or a report being applied holds a memory trim, read
`${CLAUDE_PLUGIN_ROOT}/skills/adopt/references/memory.md` first (with `${CLAUDE_PLUGIN_ROOT}/reference/drain.md` and
`${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md`): which directory each store belongs to, a moved project, placing,
and clearing.

## The four rules, and they hold in every phase

1. **Classification is by content, never by filename.** A file called `gotchas.md` whose sections are
   `## Architecture`, `## Frontend` and `## Build` is three files wearing one name, and the plan says
   so. A target justified only by a filename is not a finding.
2. **Nothing is invented.** An inference is shown as one, never as a measurement. An entry keeps its text. Nothing gains a source, a date or a
   confidence; a split separates the source's own words, and only a trigger line is new text, shown
   beside what it replaces.
3. **Into the store it only ever copies.** Every source file outside the store is left
   byte-identical, **except for a confirmed trim of content that is already in the store, or that the lean pass
   dropped** - and a trim removes lines: it never rewords, reorders or reformats what stays, and never
   moves or deletes a file. Content that maps nowhere is named in the report with
   its verdict and reason - never dropped silently and never silently merged.
4. **Only what a root or a reference names is read** - `<path>`, the context files, the memory
   stores, and the reference graph out of them, root by approved root. **Never a file nothing pointed at**, and
   nothing outside the store is ever a target for an entry.

## Credentials are never copied

Per `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`. **A credential found in a source is reported under its own heading and never
trimmed** - the line stays exactly where it is.

## Contradictions are recorded, settled when cheap, and trimmed

Two claims that disagree - between sources, or between a source and the store - are settled per
`${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`'s *A contradiction*, for `adopt`: both written,
settled from the material or the store or one light read, else both unsettled - and the trim
follows either way. Name every pair in the report, with how it was settled or the check that would
settle it.

## Report

Say what you built, and what you changed. A header - the command, the store, the date, the
arguments, and the narrowing answer or that the default was taken - then five parts, in
this order:

1. **Inventory** - what was there, by count and size.
2. **What was written** - every file with its entry count, by absolute path. Group by target so the
   shape of the store is visible. **Then what the lean pass dropped, merged or moved**, each with
   its reason.
3. **What was trimmed** - every file touched, every line removed, and **the store file each one went
   to.** **Quote every removed line**, and **name the `tmp/adopt-<YYYYMMDD>-<n>/` folder holding the
   copies** - a revert is copying a file back. Every file that could not be trimmed, and why. If the
   trim was declined or skipped, say so.
4. **Unmapped, excluded, skipped, unplaced and contradictory** - everything that matched no target,
   every credential exclusion, every reference you could not or did not follow, every source `adopted.md` skipped, every entry for a
   project this store does not hold with where it belongs and the next step, every read line
   pointing at another store, every contradictory pair, and every *kept* line of a context file, each
   with a reason. This section is
   never empty by omission; if there is nothing, say so.
   **Then *Left for the next run*** - every file recorded `pending`, with what pointed at it, how
   much this run read against `adopt_max_bytes`, and that the next `/nk:adopt` continues there.
5. **What to look at first** - the targets where confidence was lowest, the unsettled pairs, and the
   `verified: unknown` count.

Every count follows `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md` - one numbered
list, the count is its last number, and a split prints its arithmetic. **What you wrote and what you
removed are counted after doing them**, never from the proposal.

### The proposal is grouped by category

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
to write (A-J), 60 to trim (K-M), 20 with nothing to write*, and the saved report's path. Every
item is in the saved report; the view question is how they are read.

The page is the saved report as `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md` carries it - the
view question's *Publish as a page*.
