---
name: adopt
description: Read the notes and context files the user already has - CLAUDE.md files, docs, memory - build the store from them, and trim what moved. Use when the user asks to import or adopt existing notes into Notekeeping.
argument-hint: "[path] [--apply [<report>] [all | <numbers> | <text>]] [--dry-run] [--page | --no-page] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact
---

Read what you already have - a notes folder, a wiki export, another tool's store, your `CLAUDE.md`
files - and build the store out of it. Every question this skill asks - the narrowing, a wave
of new roots, the report question, the walk - follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`,
with `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` when nobody can answer. **This skill runs
no shell and no git**: what it needs from a repository it reads from `.git` with the file tools.

Before anything else, in order:
1. **`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
2. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`.
3. **An overlay?** If `<store>/schema/skills/adopt/` exists, follow `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`: a `SKILL.md` there replaces the rest of this file, and a file under its `references/` replaces the shipped reference of that name wherever this skill cites it.
4. **Infer before asking**: read what the conversation already states, and ask only what is still open.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:adopt`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

**A re-entry? Decide before any read.** Where the conversation holds this skill's **report question**
with an answer typed after it, and the report that question named has no `## Applied`, **the material
is not read again**: read that report, append the answer as `## Answers` keyed by item number, show
each item's resolved write, and ask the report question again - the report shape's rules. Where it
holds the **narrowing** or **a wave's** question with a typed answer, the pass starts again from
phase 1 with that answer applied - the inventory is cheap - and the report's header says which roots
were dropped. Otherwise the run is fresh.

**No store means refuse and name `/nk:init`.** That command creates the store; this one fills it. Resolve every
file definition you write through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, so the
user's overlay wins here exactly as it does everywhere else.

## The two forms

| Form | Starts from |
|---|---|
| `/nk:adopt` | your context files. Every `CLAUDE.md` and `CLAUDE.local.md` the session loads is a root, their references are the next wave, and the chase runs from there |
| `/nk:adopt <path>` | that path as well. For a pile nothing happens to reference |

The bare form is the ordinary one, because your context files are where the pointers already
are. `<path>` adds a root; it never replaces the context files, which are small, always relevant,
and the only place a trim can reach. **A `<path>` inside a `.notekeeping` directory is refused by
name** - this store, `~/.notekeeping`, or any other: a store is never adopted from.

**Never start from a directory nobody named.** No scanning for note-shaped folders, no adopting a
`notes/` because of what it is called - `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` forbids
inferring from the filesystem, and it holds here. Every root is either typed by the user or written
in something you read.

**Started by Claude rather than typed, a read outside the working directory asks for permission** -
the context files above it, the walk for `.git`, `~/.claude/CLAUDE.md`. That is expected: let each
prompt come, and never work around one - no shell, no other path to the same file. A refused read is
a file this run did not read, named in the report.

## One confirmation

The unit of consent is the store, not the entry. Show what you are about to build - the targets,
their counts, what was excluded and why - then write the whole thing on one go-ahead.

What makes one confirmation safe is rule 3 below: everything written into the store is a copy.
The worst case is a store you delete and run again, with your own material untouched. **The one
exception is the trim**, which is why it is shown line by line before the question is asked, and why
every trimmed file is copied whole into the store's `tmp/` before it is touched.

## Nothing is written without a yes

The bare command reports the inventory and lets you narrow it (phase 1), then reads, classifies,
shows both halves - what would be written into the store, and what would be trimmed - saves that as
a report, and asks the report question once - *Apply / Apply and publish / Change answers / Not
now*, per `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s `## A yes applies what was shown`.
*Apply* writes both halves exactly as the report holds them, its `## Answers` included. *Not now*
leaves the report for a later `--apply`. With no turn to answer in, it writes nothing, and the saved
report is what a later `--apply` applies.

`--dry-run` shows both halves, does not ask, and saves no report. Worth typing on a large or
unfamiliar pile, to see the plan before committing to it.

`--apply [<report>] [all | <numbers> | <text>]` applies a saved report, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s *The report is saved* - the latest when none is
named; with no selection it walks the items, one `AskUserQuestion` each. It never reads your material
again: the report is what gets written.

The trim is items like any other. Its lines are in the report line by line, so `all` includes
them, numbers can name them, and text can narrow to or away from them - *"skip the trim"*. Every
trimmed line is one the report showed, and **the four rules below hold whatever the selection is**:
nothing is removed that did not reach the store first.

**One exception: `all` never trims `~/.claude/CLAUDE.md`.** It is the user's own file,
so a trim of its lines happens only when picked by number, and the proposal says so.

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
| **Nothing** | Nothing to do - say so and name `/nk:init` |
| **A pile of markdown** - a notes folder, a wiki export, a vault | The whole pass below |
| **`CLAUDE.md` files and little else** | Read them as sources, write what is knowledge, and offer to trim what moved |
| **Another tool's store** | Map its convention onto the schema where it is unambiguous, and name what is not |
| **An older version of this store** | A schema upgrade, not an adoption - **say so, name `/nk:upgrade`, and stop** |

## The pass

| Phase | Does | Writes |
|---|---|---|
| **1 · Inventory** | Walk what you were pointed at, plus the context files below - and collect the references they name. Files, sizes, formats. Do not assume markdown | no |
| **2 · Classify** | Read content, and test every file and every section against each definition's admission and exclusion tests - never a `bookkeeping` one | no |
| **3 · Plan** | The store you are about to build: every target, its entry count, everything excluded - and the trim, listed separately, with each file's trimmability | no |
| **4 · Confirm** | The report saved, then the report question, covering the store and the trim as shown | the report only |
| **5 · Build** | Create the files and write the entries | **yes, in the store** |
| **6 · Trim** | Copy each file to be trimmed into `tmp/`, then remove from it only what reached the store, and only what the yes covered | **yes, outside the store** |
| **7 · Report** | What was written, what was removed, what was not, and what needs a human eye | no |

Phase 1 is cheap and phase 2 is not. Report the inventory first - how many files, what formats,
how much text - and let the user narrow before any content is read, with one `AskUserQuestion`.
Dropping a root from a wave is how scope is cut. Where the pile is large, offer a sample in the
same question - *read all of it, or classify ~30 files first to see whether the targets fit?* - and
on that pick classify a stratified sample spread across the directories and formats the inventory
found. **Say it was a sample, and never present a sampled plan as a complete one.**

With no turn to answer either question, take the default: every root found stands, none is
dropped, and no sample is taken - phase 2 classifies everything the inventory found. The report's
header records both answers - or that the defaults were taken.

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

Every entry keeps the format its definition specifies, including the provenance stamp. Where a
`verified:` stamp cannot be established from what you read it is `verified: unknown` - visible,
never guessed.
A write that leaves a file with a numeric `budget:` at or past `budget_notice_pct` of it says so, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`.

Then `index.md` per `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` if any work item was
written. This command writes no read line: registration does, and the notes it builds are what the lines point at.

## Context files - the other source, and the only edit this command makes

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

Every entry is placed by what it is about, never by the file it came from. No source maps to a
target: `~/.claude/CLAUDE.md` mostly lands in global, but a line about one project goes to that
project, and a file above the workspace root is sorted the same way. What is needed in every session
goes to that scope's `NOTES.md`; the machine itself - installed tools, paths, versions, scripts,
layout - goes to global's `environment.md`; what is needed only sometimes goes to the register it
belongs to - `process.md` for how work moves, and `gotchas.md`, `decisions.md`, `domain.md` for the
rest - where it costs nothing until read; an instruction goes to the `instructions.md` of the scope
it holds at. Do not skip global because a source file is large: it is where the always-needed
half comes back.

**An entry about a project this store does not hold stays where it is, untrimmed.** Say where it
belongs - a workspace in global's `## Workspaces` and that store's `## Projects`, probed before it is
named per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`, or *no store holds it* - and suggest
the next step: `/nk:init` to register the workspace or the project if it is not, then `/nk:adopt`
there. **A suggestion only**: this command never writes outside this store, and nothing but `/nk:init`
creates a project. An entry that names no project is placed by content as any other.

One source of truth is the point. A fact copied into the store and left in a context file is now
in two places, drifting, and paying always-loaded cost in one of them. So the move is finished
rather than half-made.

### Follow what they point at, all the way

Keep chasing. A context file points at a notes repo; that repo's index points at three
directories; one of those points at a runbook somewhere else again. All of it is the material,
and a traversal that stops early builds a store with holes in it. References are followed from
anything you read, not only from context files, and until nothing new is found.

1. An explicit reference, never an inference. A path, directory or repository written in the
   text. Not a directory that looked promising, and not a sibling you noticed on the way past.
   **Never one that leads into a `.notekeeping` directory** - not followed, and named.
2. Keep a visited set, keyed by resolved absolute path, and read nothing twice. That is what
   makes a cycle safe - two files pointing at each other terminate because the second read never
   happens, not because a counter ran out.
3. Consent is per root, not per file. `[path]` and the context files are the first roots.
   Anything resolving inside an approved root is read freely. A reference resolving outside
   every approved root is a new root: collect the whole wave, report each with its size and what
   pointed at it, and let the user drop any before you read them - one `AskUserQuestion` per
   wave, and only that. With no turn to answer, read the whole wave - nothing in it is dropped.
   A typed answer re-enters and the chase is walked again from phase 1 with it applied; say so.
4. Repeat until the frontier is empty. Each wave can produce the next; keep going until a wave
   turns up nothing new. Depth is whatever the material is - there is no cap, because a cap is an
   arbitrary place to start losing knowledge.
5. **What cannot be read is named, never guessed.** A URL, a wiki link, a path that does not exist, a
   repository you cannot reach, a read refused: report it under unfollowed references, with what
   pointed at it. This command has no network access and must not imply otherwise.

**Every followed pile is a source and never a target.** The trim below reaches context files only -
nothing inside a followed directory is edited, moved or removed, however much is copied out of it.
This matters more the further the chase goes.

### What moves, and what stays

Routing a candidate - admission, scope, already there - is per `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`.

Both move, and they go to different files. A fact you *look up* - a port, a name, a trap, a
decision and its reason - goes to `NOTES.md` or the register it belongs to. A directive you *obey* -
house style, a workflow rule, *always run the linter* - goes to the `instructions.md` of the scope it
holds at: the project, the workspace, or global for one that holds everywhere. **Never merge the
two**: a fact filed as an instruction becomes unfalsifiable, and an instruction filed as a fact
becomes optional. The test is the schema's own
(`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/global/NOTES.md`).

**Every instruction moves, whatever its scope's budget.** It was already read on every prompt
from the file it came from, so a move past the ceiling relocates that cost rather than adding to it,
and the trim removes the old copy. The write says so per the budget notice, above, and the report
names each `instructions.md` it left past its ceiling. **Never shorten or reword an instruction on
the way in**: a reworded rule is a different rule, and nobody approved it.

### The trim invariant

**A line is removed only when its content is in the store, and the report names the file it went
to.** Nothing here is ever deleted - a trim only finishes a move. Content that mapped nowhere
stays exactly where it is, and so does anything you could not place with confidence.

Whether a file can be trimmed depends on where it is, decided by reading, never by running git:

| Where the file is | Trimmed? |
|---|---|
| **In no work tree** - `~/.claude/CLAUDE.md` on most machines, a workspace root that is no repository | **yes** |
| **In a work tree**, named `CLAUDE.md` or `CLAUDE.local.md`, and **ignored** | **yes** |
| **In a work tree**, anything else | **never** - the report quotes the lines that moved and says the file is yours to trim |

The work tree is found from the file's own directory by walking up, per
`${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`'s *Which repository* - `Read` `<dir>/.git/HEAD`, else
`<dir>/.git` as a file, at each level to the filesystem root. **Never `Glob` for it**: a `Glob` rooted at
the filesystem root searches the whole disk. Found nothing to the root: the file is in no work tree.

Ignored means: `.git/info/exclude` - located as `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`'s
ignore step locates it, through `gitdir:` and `commondir` - or the repository root's `.gitignore`
holds a line equal to `/<path from the repo root>`, or the bare file name for a file at the root,
and neither file holds any `!` line. A pattern, a nested `.gitignore`, `core.excludesFile` and the
global ignore file are not read: the file is untrimmable, and the report says why. `/nk:init` writes
`/CLAUDE.local.md` - or `/<rel>/CLAUDE.local.md` - which this recognises. A file committed and later
ignored is trimmed: in the working tree, where the diff shows it.

**A read that is refused, or a walk that cannot finish, makes the file untrimmable** - say which, and
why. Never trim on a guess.

**The trim copy comes first.** Before the first line of a file is removed, write the file whole into
`.notekeeping/tmp/adopt-<YYYYMMDD>-<n>/` - the same `<n>` as this run's report - at its path from its
root: `home/` for a file under `~`, `ws/` under the workspace root, `repo-<name>/` under a registered
repository, whichever is nearest. **The report's trim section names the folder and says a revert is
copying a file back.** No skill empties or deletes these copies. **The report quotes every line it
removes** as well - the copy is the way back, the quote is what the yes was given to.

### The trim is shown in full, and the same yes covers it

Everything else is a copy into a directory you can delete; this is the one thing that changes a file
you wrote. Show the lines, grouped by file, inside the proposal - never a count in their place -
so the yes that approves the store has seen them, and with each file's trimmability from the
table above. Leaving it out is a normal answer: *"yes, but don't trim"* builds the store and
removes nothing, and you can trim later with `--apply <report> <trim items>`, naming the trim's
numbers from the same saved report.

## The four rules, and they hold in every phase

1. **Classification is by content, never by filename.** A file called `gotchas.md` whose sections are
   `## Architecture`, `## Frontend` and `## Build` is three files wearing one name, and the plan says
   so. A target justified only by a filename is not a finding.
2. **Nothing is invented.** An entry keeps its text and its provenance - **or its lack of
   provenance.** Nothing gains a source id it did not earn; a bulk stamp would destroy the one health
   metric the store has.
3. **Into the store it only ever copies.** Every source file outside the store is left
   byte-identical, **except for a confirmed trim of content that is already in the store** - and a
   trim removes, never rewrites. Content that maps nowhere is named in the report, never dropped
   and never silently merged.
4. **Only what a root or a reference names is read** - `[path]`, the context files, and the
   reference graph out of them, root by approved root - plus the `.git` files the trim's walk
   reads. **Never a file nothing pointed at**, and nothing outside the store is ever a target for an
   entry.

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
arguments, and the narrowing and wave answers or that the defaults were taken - then five parts, in
this order:

1. **Inventory** - what was there, by count and size.
2. **What was written** - every file with its entry count, by absolute path. Group by target so the
   shape of the store is visible. Name the roots you traversed, and for any beyond the first,
   what pointed at them.
3. **What was trimmed** - every file touched, every line removed, and **the store file each one went
   to.** **Quote every removed line**, and **name the `tmp/adopt-<YYYYMMDD>-<n>/` folder holding the
   copies** - a revert is copying a file back. Every file that could not be trimmed, and why. If the
   trim was declined or skipped, say so.
4. **Unmapped, excluded, unfollowed, unplaced and contradictory** - everything that matched no target,
   every credential exclusion, every reference you could not or did not follow, every entry for a
   project this store does not hold with where it belongs and the next step, every read line
   pointing at another store, and every contradictory pair, each with a reason. This section is
   never empty by omission; if there is nothing, say so.
5. **What to look at first** - the targets where confidence was lowest, the unsettled pairs, and the
   `verified: unknown` count.

Every count follows `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md` - one numbered
list, the count is its last number, and a split prints its arithmetic. **What you wrote and what you
removed are counted after doing them**, never from the proposal.

The report can be a page - the report question's *Apply and publish*, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`, which owns the two flags and what the page may
carry. The terminal report is printed either way.

Under `--oneline` this report goes to the file the outcome line names, per the contract.

## Never

- **Never trim a file in a work tree** other than an ignored `CLAUDE.md` or `CLAUDE.local.md`, and
  never trim one whose trimmability a refused read left undecided.
- **Never remove a line whose content is not in the store** - an instruction included: it is removed
  only once it is in an `instructions.md`, under the same trim invariant as a fact.
- **Never touch a file before its trim copy is written**, and never empty or delete a trim copy.
- **Never rewrite a context file.** A trim removes lines; it does not reword, reorder or reformat
  what stays.
- **Never write outside the store** other than a confirmed trim.
- **Never import from, or trim a line that points into, a `.notekeeping` directory.**
- **Never move or delete a file**, only lines within one.
- **Never follow a reference the user declined**, never read the same path twice, and **never edit
  anything inside a followed pile.**
- **Never classify by filename**, and never present an inference as a measurement.
- **Never invent provenance, a date, or a confidence.**
- Never read outside what you were pointed at, the context files the session loads, and the `.git`
  files the trim's walk needs.
