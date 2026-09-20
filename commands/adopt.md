---
description: Read the notes and context files you already have and build the store from them, leaving one source of truth.
argument-hint: "[path] [--apply [<text>]] [--dry-run] [--sample <n>] [--page | --no-page] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git rev-parse:*), Bash(git ls-files:*), Bash(git diff:*), Artifact
---

Read what you already have - a notes folder, a wiki export, another tool's store, your `CLAUDE.md`
files - and **build the store out of it.** This is the migration: point it at years of accumulated
material and get a populated `.notekeeping/`, not a list of chores.

**Resolve the store first**, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md` - **no store
means refuse and name `/nk:init`.** That command creates the store; this one fills it. Resolve every
file definition you write through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, so the
user's overlay wins here exactly as it does everywhere else.

**`--caller <name>`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: **report the
inventory, and write nothing.** Filling a store from material nobody has looked at is a person's
act, and the detail says so rather than naming an argument that would unlock it.

## The two forms

| Form | Starts from |
|---|---|
| `/nk:adopt` | **your context files.** Every `CLAUDE.md` and `CLAUDE.local.md` in scope is a root, their references are the next wave, and the chase runs from there |
| `/nk:adopt <path>` | **that path as well.** For a pile nothing happens to reference |

**The bare form is the ordinary one**, because your context files are where the pointers already
are. `<path>` **adds** a root; it never replaces the context files, which are small, always relevant,
and the only place a trim can reach.

**Never start from a directory nobody named.** No scanning for note-shaped folders, no adopting a
`notes/` because of what it is called - `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md` forbids
inferring from the filesystem, and it holds here. Every root is either typed by the user or written
in something you read.

## One confirmation, not two hundred

**The unit of consent is the store, not the entry.** Show what you are about to build - the targets,
their counts, what was excluded and why - then write the whole thing on one go-ahead. A per-entry
prompt across a real notes folder is a thousand questions nobody answers, and it is how a migration
command becomes one nobody runs.

**What makes one confirmation safe is rule 3 below**: everything written into the store is a copy.
The worst case is a store you delete and run again, with your own material untouched. **The one
exception is the trim, and it takes a second yes of its own.**

## Nothing is written without `--apply`

**The bare command behaves as `--dry-run`:** it reads, classifies, and shows both halves - what
would be written into the store, and what would be trimmed - and **writes nothing at all.** So the
form a person types first is always the safe one, and `--apply` is the only spelling that writes.

**`--dry-run` remains, and means exactly the same thing.** It is worth typing when you want the
intent on the record, and on a large or unfamiliar pile.

**`--apply` authorises the store build. The trim still takes its own second yes**, after its own
report - the flag does not carry it, and no flag ever does.

### `--apply <text>`

**Free text narrows and disambiguates, within what this run just proposed.** *"Only the gotchas"*,
*"skip the wiki export"*, *"treat the ADR folder as decisions"* - it picks from the proposals in
front of it, and it can supply a judgement a classification was missing.

**It cannot reach past the proposal list**, it never authorises the trim, and **the four rules below
hold whatever it says.** Text asking for something outside them is refused by name, and the rest of
the instruction is still honoured. **Say which writes the proposal earned on its own and which the
text authorised.**

## The five starting states

| You have | This does |
|---|---|
| **Nothing** | Nothing to do - say so and name `/nk:init` |
| **A pile of markdown** - a notes folder, a wiki export, a vault | The whole pass below |
| **`CLAUDE.md` files and little else** | Read them as **sources**, write what is knowledge, and offer to trim what moved |
| **Another tool's store** | Map its convention onto the schema **where it is unambiguous**, and name what is not |
| **An older version of this store** | A schema upgrade, not an adoption - **say so, name `/nk:upgrade`, and stop** |

## The pass

| Phase | Does | Writes |
|---|---|---|
| **1 · Inventory** | Walk what you were pointed at, plus the context files below - and collect the references they name. Files, sizes, formats. **Do not assume markdown** | no |
| **2 · Classify** | Read **content**, and test every file and every section against each definition's admission and exclusion tests | no |
| **3 · Plan** | The store you are about to build: every target, its entry count, everything excluded - and the trim, listed separately | no |
| **4 · Confirm** | **One decision for the store. A second for the trim** | no |
| **5 · Build** | Create the files and write the entries | **yes, in the store** |
| **6 · Trim** | Remove from the context files only what reached the store, and only if confirmed | **yes, outside the store** |
| **7 · Report** | What was written, what was removed, what was not, and what needs a human eye | no |

**Phase 1 is cheap and phase 2 is not.** Report the inventory first - how many files, what formats,
how much text - and **let the user narrow before any content is read.** Dropping a root from a wave
is how scope is cut; **`--sample <n>` classifies a stratified sample** spread across the directories
and formats the inventory found, for when the question is *do these targets fit my material at all*.
**Say it was a sample, and never present a sampled plan as a complete one.**

## What it writes into the store

**Registers and ledgers, and the two project documents** - `gotchas.md`, `patterns.md`,
`decisions.md`, `domain.md`, `runbook.md`, `architecture.md`, and a project's `NOTES.md` and
`overview.md` where the material supports them.

**Write only what the material earns.** A target with no admitted entry is not created: an empty
register is a stub, and a stub looks answered. `/nk:init` follows the same rule.

**Every entry keeps the format its definition specifies**, including the provenance stamp. Where a
`verified:` stamp cannot be established from what you read it is **`verified: unknown`** - visible,
never guessed.

**Then `index.md`** per `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` if any work item was
written, and the projections per `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - **store first,
projections last.**

## Context files - the other source, and the only edit this command makes

**Read every `CLAUDE.md` and `CLAUDE.local.md` in scope** - the repository's, the workspace's, and
your own - as sources like any other pile. They are usually where knowledge went when there was
nowhere better to put it.

**One source of truth is the point.** A fact copied into the store and left in a context file is now
in two places, drifting, and paying always-loaded cost in one of them. So the move is finished
rather than half-made.

### Follow what they point at, all the way

**Keep chasing.** A context file points at a notes repo; that repo's index points at three
directories; one of those points at a runbook somewhere else again. **All of it is the material**,
and a traversal that stops early builds a store with holes in it. References are followed **from
anything you read**, not only from context files, and **until nothing new is found.**

1. **An explicit reference, never an inference.** A path, directory or repository written in the
   text. Not a directory that looked promising, and not a sibling you noticed on the way past.
2. **Keep a visited set, keyed by resolved absolute path**, and read nothing twice. **That is what
   makes a cycle safe** - two files pointing at each other terminate because the second read never
   happens, not because a counter ran out.
3. **Consent is per root, not per file.** `[path]` and the context files are the first roots.
   Anything resolving **inside** an approved root is read freely. A reference resolving **outside**
   every approved root is a new root: collect the whole wave, report each with its size and **what
   pointed at it**, and let the user drop any before you read them.
4. **Repeat until the frontier is empty.** Each wave can produce the next; keep going until a wave
   turns up nothing new. **Depth is whatever the material is** - there is no cap, because a cap is an
   arbitrary place to start losing knowledge.
5. **What cannot be read is named, never guessed.** A URL, a wiki link, a path that does not exist, a
   repository you cannot reach: report it under unfollowed references, **with what pointed at it.**
   **This command has no network access** and must not imply otherwise.

**Every followed pile is a source and never a target.** The trim below reaches context files only -
nothing inside a followed directory is edited, moved or removed, however much is copied out of it.
**This matters more the further the chase goes.**

### What moves, and what stays

**Instructions are not knowledge.** A directive you *obey* - house style, a workflow rule, *always
run the linter* - **stays exactly where it is, whatever its git state.** What moves is a fact you
*look up*: a port, a name, a trap, a decision and its reason. That test is the schema's own
(`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/global/NOTES.md`), and it is the whole difference
between tidying a context file and gutting it.

### The trim invariant

**A line is removed only when its content is in the store, and the report names the file it went
to.** Nothing here is ever deleted - **a trim only finishes a move.** Content that mapped nowhere
stays exactly where it is, and so does anything you could not place with confidence.

| The content | Trim |
|---|---|
| **at `HEAD`** - committed, tracked | **never, under any flag.** Other people read and review that file |
| **not at `HEAD`**, in a tracked file | yes, once it is in the store - and **the report quotes every line removed** |
| **an untracked file**, at any path | yes, once it is in the store - and **the report quotes every line removed**, because git holds nothing to recover |

**The report quotes every line it removes, in both trimmable rows.** A line that is only in the
working tree is recoverable from `git diff`, and one that is **staged** is recoverable from the
index and not from `git diff` at all - so the recovery path differs per line and the user cannot be
expected to know which they have. **Quoting is the one thing that does not depend on knowing.**

**Compute this; never estimate it.** `git ls-files --error-unmatch <path>` says whether a file is
tracked. **The removable set is `git diff -- <path>` *and* `git diff --cached -- <path>` together** -
the first is what is unstaged, the second is what is staged, and **a staged line appears only in the
second**. Reading only `git diff` would classify a staged line as *at `HEAD`* and refuse to trim it,
or classify it as recoverable by a command that does not show it. **With git unavailable you cannot
tell those three apart - so trim nothing, and say why.**


**The report can be a page.** Where there is enough to choose between, offer it at the very
end, per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md` - which owns the offer, the two
flags, and what the page may carry. **The terminal report is printed either way.**

### The trim is confirmed separately

**The store build and the trim are two questions.** Everything else is a copy into a directory you
can delete; this is the one thing that changes a file you wrote. **Show the lines, grouped by file,
and take a second yes.** Declining is a normal answer - the store is built either way, and you can
run the trim later by running the pass again.

## The four rules, and they hold in every phase

1. **Classification is by content, never by filename.** A file called `gotchas.md` whose sections are
   `## Architecture`, `## Frontend` and `## Build` is three files wearing one name, and the plan says
   so. **A target justified only by a filename is not a finding.**
2. **Nothing is invented.** An entry keeps its text and its provenance - **or its lack of
   provenance.** Nothing gains a source id it did not earn; a bulk stamp would destroy the one health
   metric the store has.
3. **Into the store it only ever copies.** Every source file outside the store is left
   byte-identical, **except for a confirmed trim of content that is already in the store** - and a
   trim removes, never rewrites. Content that maps nowhere is **named in the report**, never dropped
   and never silently merged.
4. **Only what a root or a reference names is read** - `[path]`, the context files, and the
   reference graph out of them, root by approved root. **Never a file nothing pointed at**, and
   nothing outside the store is ever a target for an entry.

## Credentials are never copied

**A faithful copy copies secrets.** Anything lifted into the store can reach a projection, and a
projection is loaded into every prompt in that repository - **and nothing un-reads a secret that has
been in a context window**, so this is a gate rather than a caution.

1. **Never write content from a file or section that looks like it holds a credential** - a key, a
   token, a password, a connection string carrying one, a private key block.
2. **Be wrong in one direction only.** Excluding something harmless costs a line in the report;
   including a credential cannot be undone.
3. **An exclusion is not a row anyone can say yes to.** Neither confirmation reaches it. Report it,
   with its path and the reason, under its own heading.
4. **An excluded line is never trimmed.** It did not reach the store, so the invariant forbids it -
   and a secret deleted from the only file holding it is the worst outcome available here.

## Contradictions are surfaced, never resolved

Two sources disagreeing is a finding, not a merge conflict to settle. **Write both, cite both, and
name the pair in the report.** Deciding which of two things the user wrote is true is not this
command's call, and a migration that quietly picks a winner does it in the least recoverable place.
**Neither side is ever trimmed**, because neither is settled.

## Report

**Say what you built, and what you changed.** Five parts, in this order:

1. **Inventory** - what was there, by count and size.
2. **What was written** - every file with its entry count, by absolute path. Group by target so the
   shape of the store is visible. **Name the roots you traversed**, and for any beyond the first,
   what pointed at them.
3. **What was trimmed** - every file touched, every line removed, and **the store file each one went
   to.** Quote the removed lines for any untracked file. If the trim was declined or skipped, say so.
4. **Unmapped, excluded, unfollowed and contradictory** - everything that matched no target, every
   credential exclusion, every reference you could not or did not follow, and every contradictory
   pair, each with a reason. **This section is never empty by omission**; if there is nothing, say
   so.
5. **What to look at first** - the targets where confidence was lowest, and the `verified: unknown`
   count.

**Every count follows `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`** - one numbered
list, the count is its last number, and a split prints its arithmetic. **What you wrote and what you
removed are counted after doing them**, never from the proposal.

**Under `--caller` none of this applies**: the outcome line is one line, and nothing else.

## Never

- **Never touch anything at `HEAD`**, in any file, under any flag. That is the permanent one.
- **Never remove a line whose content is not in the store**, and never remove an instruction.
- **Never rewrite a context file.** A trim removes lines; it does not reword, reorder or reformat
  what stays.
- **Never write outside the store** other than the projections and a confirmed trim.
- **Never move or delete a file**, only lines within one.
- **Never follow a reference the user declined**, never read the same path twice, and **never edit
  anything inside a followed pile.**
- **Never classify by filename**, and never present an inference as a measurement.
- **Never invent provenance, a date, or a confidence.**
- Never read outside what you were pointed at and the context files in scope.
