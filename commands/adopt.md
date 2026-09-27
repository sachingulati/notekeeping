---
description: Read the notes and context files you already have and build the store from them, leaving one source of truth.
argument-hint: "[path] [--apply [<report>] [all | <numbers> | <text>]] [--dry-run] [--page | --no-page] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git rev-parse:*), Bash(git ls-files:*), Bash(git diff:*), Bash(git -C:*), Artifact
---

Read what you already have - a notes folder, a wiki export, another tool's store, your `CLAUDE.md`
files - and **build the store out of it.** This is the migration: point it at years of accumulated
material and get a populated `.notekeeping/`, not a list of chores.

**Resolve the store first**, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md` - **no store
means refuse and name `/nk:init`.** That command creates the store; this one fills it. Resolve every
file definition you write through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, so the
user's overlay wins here exactly as it does everywhere else.

**`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

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
exception is the trim**, which is why it is shown line by line before the question is asked.

## Nothing is written without a yes

**The bare command reports the inventory and lets you narrow it (phase 1), then reads, classifies,
shows both halves - what would be written into the store, and
what would be trimmed - saves that as a report, and asks once for the writes.** A yes writes both
halves exactly as shown, per `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s `## A yes applies what
was shown`. With no turn to answer in, it writes nothing, and the saved report is what a later
`--apply` applies.

**`--dry-run` shows both halves and does not ask.** It is worth typing when you want the intent on
the record, and on a large or unfamiliar pile.

**`--apply [<report>] [all | <numbers> | <text>]` applies a saved report**, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s *The report is saved* - the latest when none is named. It never reads your material again:
the report is what gets written.

**The trim is items like any other.** Its lines are in the report line by line, so `all` includes
them, numbers can name them, and text can narrow to or away from them - *"skip the trim"*. Every
trimmed line is one the report showed, and **the four rules below hold whatever the selection is**:
nothing is removed that did not reach the store first.

### `--apply <text>`

**Free text narrows and disambiguates, within the saved report.** *"Only the gotchas"*, *"skip the
wiki export"*, *"treat the ADR folder as decisions"* - it picks from the proposals the report carries,
and it can supply a judgement a classification was missing.

**It cannot reach past the report**, and **the four rules below
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
| **4 · Confirm** | **One question, covering the store and the trim as shown** | no |
| **5 · Build** | Create the files and write the entries | **yes, in the store** |
| **6 · Trim** | Remove from the context files only what reached the store, and only what the yes covered | **yes, outside the store** |
| **7 · Report** | What was written, what was removed, what was not, and what needs a human eye | no |

**Phase 1 is cheap and phase 2 is not.** Report the inventory first - how many files, what formats,
how much text - and **let the user narrow before any content is read.** Dropping a root from a wave
is how scope is cut. **Where the pile is large, offer a sample in the same breath** - *read all of
it, or classify ~30 files first to see whether the targets fit?* - and on a yes classify a stratified
sample spread across the directories and formats the inventory found. **Say it was a sample, and
never present a sampled plan as a complete one.**

## What it writes into the store

**Registers and ledgers, and the two project documents** - `gotchas.md`, `patterns.md`,
`decisions.md`, `domain.md`, `runbook.md`, `architecture.md`, `process.md`, global's
`environment.md`, each scope's `NOTES.md` and `instructions.md`, and a project's `overview.md` where
the material supports them.

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

**Your own is `~/.claude/CLAUDE.md`, and its knowledge goes to global.** Distil it the way any pile
is distilled, never copy it across: what is needed in every session goes to `~/.notekeeping/NOTES.md`, which
the global projection carries back into every session under `~`; the machine itself - installed
tools, paths, versions, scripts, layout - goes to `environment.md`, which the same projection carries
under `## Environment`; what is needed only sometimes goes to the global register it belongs to -
`process.md` for how work moves - review, release, testing and
deployment across projects - and `gotchas.md`, `decisions.md`, `domain.md` for the rest - where it
costs nothing until read. Its instructions go to `~/.notekeeping/instructions.md`, which the same projection
carries. A fact or an instruction that holds in one workspace or one project goes there instead,
not to global. **Do not skip the global projection** because the source file is
large: it is where the always-needed half comes back.

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

**Both move, and they go to different files.** A fact you *look up* - a port, a name, a trap, a
decision and its reason - goes to `NOTES.md` or the register it belongs to. A directive you *obey* -
house style, a workflow rule, *always run the linter* - goes to the `instructions.md` of the scope it
holds at: the project, the workspace, or global for one that holds everywhere. **Never merge the
two**: a fact filed as an instruction becomes unfalsifiable, and an instruction filed as a fact
becomes optional. The test is the schema's own
(`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/global/NOTES.md`).

**An instruction moves only if it fits.** Every `instructions.md` has a byte budget, and the
projection renders it whole. **Where the instructions that hold at one scope do not fit, move what
fits, leave the rest exactly where it was, and say so in the report** - naming what stayed and the
budget it would have broken. Never shorten an instruction to make it fit: a reworded rule is a
different rule, and nobody approved it.

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

**Compute this; never estimate it** - and **where git cannot answer** - refused, unavailable, or a
repository you cannot run it in - **the file is untrimmable**, exactly as if it were at `HEAD`.
**A file in no work tree is untracked, and that is an answer, not a failure**: `git rev-parse
--is-inside-work-tree` run in its directory reports that it is outside one, so there is no `HEAD` and
no index to hold it. It takes the untracked row. This is what makes `~/.claude/CLAUDE.md` trimmable
on most machines. Only a refusal, an error or git being absent leaves a file untrimmable.
Never infer tracked state from what is inside `.git/`: that is an inference, and a trim acts on it. `git ls-files --error-unmatch <path>` says whether a file is
tracked. **The removable set is `git diff -- <path>` *and* `git diff --cached -- <path>` together** -
the first is what is unstaged, the second is what is staged, and **a staged line appears only in the
second**. Reading only `git diff` would classify a staged line as *at `HEAD`* and refuse to trim it,
or classify it as recoverable by a command that does not show it. **With git unavailable you cannot
tell those three apart - so trim nothing, and say why.**


**The report can be a page.** Where there is enough to choose between, offer it at the very
end, per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md` - which owns the offer, the two
flags, and what the page may carry. **The terminal report is printed either way.**

### The trim is shown in full, and the same yes covers it

Everything else is a copy into a directory you can delete; this is the one thing that changes a file
you wrote. **Show the lines, grouped by file, inside the proposal** - never a count in their place -
so the yes that approves the store has seen them. **Leaving it out is a normal answer**: *"yes, but
don't trim"* builds the store and removes nothing, and you can trim later by running the pass again.

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
3. **An exclusion is not a row anyone can say yes to.** No yes reaches it. Report it,
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
   to.** **Quote every removed line**, tracked file or untracked - the recovery path differs per line. If the trim was declined or skipped, say so.
4. **Unmapped, excluded, unfollowed and contradictory** - everything that matched no target, every
   credential exclusion, every reference you could not or did not follow, and every contradictory
   pair, each with a reason. **This section is never empty by omission**; if there is nothing, say
   so.
5. **What to look at first** - the targets where confidence was lowest, and the `verified: unknown`
   count.

**Every count follows `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`** - one numbered
list, the count is its last number, and a split prints its arithmetic. **What you wrote and what you
removed are counted after doing them**, never from the proposal.

**Under `--oneline` this report goes to the file the outcome line names**, per the contract.

## Never

- **Never touch anything at `HEAD`**, in any file, under any flag. That is the permanent one.
- **Never remove a line whose content is not in the store** - an instruction included: it is removed
  only once it is in an `instructions.md`, under the same trim invariant as a fact.
- **Never rewrite a context file.** A trim removes lines; it does not reword, reorder or reformat
  what stays.
- **Never write outside the store** other than the projections, their `.git/info/exclude` entry, and a
  confirmed trim.
- **Never move or delete a file**, only lines within one.
- **Never follow a reference the user declined**, never read the same path twice, and **never edit
  anything inside a followed pile.**
- **Never classify by filename**, and never present an inference as a measurement.
- **Never invent provenance, a date, or a confidence.**
- Never read outside what you were pointed at and the context files in scope.
