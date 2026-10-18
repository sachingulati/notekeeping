---
name: adopt
description: Read the notes and context files the user already has - CLAUDE.md files, docs, memory - build the store from them, and trim what moved. Use when the user asks to import or adopt existing notes into Notekeeping.
argument-hint: "[path] [--again] [--apply [<run>] [all | <numbers> | <categories> | <text>]] [--dry-run] [--page | --no-page]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact, ArtifactData
---

Read what you already have - a notes folder, a wiki export, another tool's store, your `CLAUDE.md`
files - and build the store out of it. Every question this skill asks - the narrowing, a wave
of new roots, the first and second questions, the walk - follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`. What it needs from a repository
it asks git, per `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`; a file's size and hash, and
the trim copy, are per `${CLAUDE_PLUGIN_ROOT}/reference/store/file-facts.md`. **A run with an item is
a run item** - its inventory, report, answers, log, drafts and trim copies - per
`${CLAUDE_PLUGIN_ROOT}/reference/run-engine.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md`,
`${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md` and the work definitions it names).

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs. **Then the schema release**: read each resolved store's `schema_version` and `Grep` the release in `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (*The schema release this plugin ships*). A store at it is not mentioned; one behind it is said in one line on every run, naming `/nk:upgrade`, and the run carries on; newer or absent follows that file's four answers.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:adopt`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

**A re-entry? Decide before any read.** Where the conversation holds this skill's **second question**
with an answer typed after it, and the run that question named still has an open item, **the material
is not read again**: read that run's `report.md`, write each answer onto its item, show each item's
resolved write, and ask the second question again - the report-apply rule's re-entry. Where it
holds the **first question** with an answer typed after it, read that run's report and carry out the pick -
an apply, or the view and then the second question - the material is not read again either. Where it
holds the **narrowing** or **a wave's** question with a typed answer, the pass starts again from
phase 1 with that answer applied - the inventory is cheap - and `requirements.md` says which folders
were dropped. Otherwise the run is fresh - and before it starts, a run of this command with `drafts/`
and no report is offered to resume, per the run engine's *Resuming*.

**No store means refuse and name `/nk:init`.** That command creates the store; this one fills it. Resolve every
file definition you write through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, so the
user's overlay wins here exactly as it does everywhere else.

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

## The two forms

| Form | Reads |
|---|---|
| `/nk:adopt` | your **native context** - every `CLAUDE.md` and `CLAUDE.local.md` the session loads, and **every harness memory store** (*Harness memory*, below) - then what they point at, wave by wave |
| `/nk:adopt <path>` | that path, then what it points at, the same way |

**The bare form runs first.** Native context is small, loaded in every session, and the only place a
trim can reach, so it is where adopting pays most and costs least. `<path>` is for a pile nothing
points at.

**Adopt is iterative: one run reads at most `adopt_max_bytes`, and the next run carries on.** How
the limit is counted, and what a run says when it reaches it, are `sources.md`'s *The limit*.

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

What makes one confirmation safe is rule 3 above: everything written into the store is a copy.
The worst case is a store you delete and run again, with your own material untouched. **The one
exception is the trim**, which is why the run's report holds it line by line, why it has its own
pick, and why every trimmed file is copied whole into the run's `copies/` before it is touched.

## Nothing is written without a yes

The bare command reports the inventory and lets you narrow it (phase 1), then reads, classifies and
leans, and saves both halves - what would be written into the store, and what would be trimmed - as
a report, **grouped by category** (`reporting.md`). The terminal shows the categories and their
counts. Then one question, with `AskUserQuestion`, and a second only where you chose to look first:

1. **The first question - *Apply all and trim / Apply all / Review as a page / Show here*.**

   | Pick | Does |
   |---|---|
   | **Apply all and trim** | writes every item, the store half first, then the trim - without the items being shown, for a user who trusts the report |
   | **Apply all** | writes every store item; nothing is trimmed |
   | **Review as a page** | publishes the run's report, per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`, then asks the second question |
   | **Show here** | prints every item, category by category, then asks the second question |

   **Apply all writes the report as it stands**, per `${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`'s
   `## A yes applies what was shown` - an item with a question of its own takes the answer the report
   proposes for it. *Review as a page* is not offered where that rule would offer no page - no
   publishing tool, `--no-page`; where a publish fails, print the items here and go on. `--page`
   publishes without asking. **There is no *Not now*: the run's report is written before the
   question**, so dismissing it, or typing *not now*, ends the run with it kept for a later `--apply`.
2. **The second question - *Apply / Apply and trim / Trim only / Not now*** - once the items have
   been shown:

   | Pick | Writes, exactly as the report holds it, its items' answers included |
   |---|---|
   | **Apply** | the store half; nothing is trimmed |
   | **Apply and trim** | both halves, the store first |
   | **Trim only** | the trim items whose content is already in the store, or that the lean pass dropped - an earlier apply's trim. One whose content is not in the store is skipped, and the report says so |
   | **Not now** | nothing; the run stays for a later `--apply` |

   **Where the page was published and keeps its selection**, *Apply the page's selection* takes the
   place of *Trim only* - the page's ticks name writes and trims alike - per the report-apply
   rule's pick of that name.

An answer of your own to either question - *only A and C*, *3: merge into auth* - is typed, and
comes back as a re-entry.

With no turn to answer in, neither question is asked and nothing is written; the run is what a later
`--apply` applies.

`--dry-run` shows both halves, does not ask, and mints nothing. Worth typing on a large or
unfamiliar pile, to see the plan before committing to it.

`--apply [<run>] [all | <numbers> | <categories> | <text>]` applies a run, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`'s `## --apply` - the latest adopt run when none is
named; with no selection it walks the items, one `AskUserQuestion` each. It never reads your material
again: the report is what gets written.

**An item's fingerprint** (the run engine's *Carried decisions*) is `<source path> · <the quoted
text's hash>`: a declined item comes back when the text it quoted changes, and a new target alone
does not bring it back.

The trim is items like any other. Its lines are in the report line by line, so `all` includes
them, numbers can name them, and text can narrow to or away from them - *"skip the trim"*. Every
trimmed line is one the report showed, and **the four rules above hold whatever the selection is**:
nothing is removed that did not reach the store first.

### `--apply <text>`

Free text selects and excludes, within the run's report. *"Only the gotchas"*, *"skip the
wiki export"* - it picks from the proposals the report already carries. It cannot supply a
judgement the classification was missing, since applying never reads the material again.

**It cannot reach past the report**, and **the four rules above
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
| **1 · Inventory** | Walk what this form reads (above), less what earlier adopt runs read and that is unchanged, or declined (`sources.md`), plus the pending set. Files, sizes, formats. Do not assume markdown | no |
| **2 · Classify** | Read content, and test every file and every section against each definition's admission and exclusion tests - never a `bookkeeping` one. **Read what the store already holds at every target** - a duplicate and a contradiction with the store are found here, not at the build | no |
| **3 · Lean** | Read the classified result **as a whole**, every target file together with what it already holds, and apply *Worth recording* (`placing.md`) - drop, merge, move on-demand | no |
| **4 · Plan** | The store you are about to build: every target, its entry count, everything excluded and dropped - and the trim, listed separately, with each file's trimmability | no |
| **5 · Confirm** | The run item minted with its report, then the first question, covering the store, the drops and the trim as shown | the run item only |
| **6 · Build** | Create the files and write the entries | **yes, in the store** |
| **7 · Trim** | Copy each file to be trimmed into the run's `copies/`, then remove from it only what reached the store or was dropped, and only what the yes covered | **yes, outside the store** |
| **8 · Record and report** | Bring `inventory.md` up to date - each source read, each left pending, each root declined - then `summary.md` and `resume.md`; say what was written, dropped, removed and not, what is left, and what needs a human eye | **the run item** |

These are the run engine's phases, named for what adopt does in them: 2 and 3 are its pass, 4 its
report, 5 its question, 6 and 7 its apply, 8 its record.

### What each phase reads

**This file holds the steps and the rules; the detail is in the files below.** Read each before the
phase it serves, and again when the conversation has been compacted since.

| Before | Read |
|---|---|
| **phase 1** | `${CLAUDE_PLUGIN_ROOT}/skills/adopt/references/sources.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`, `${CLAUDE_PLUGIN_ROOT}/reference/store/file-facts.md`, `${CLAUDE_PLUGIN_ROOT}/reference/run-engine.md` and `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`): the limit, the inventory and the narrowing, the context files and a source's reach, and following what they point at |
| **phase 2** | `${CLAUDE_PLUGIN_ROOT}/skills/adopt/references/placing.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`, `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/global/NOTES.md`, `${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`, `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` and `${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md`): what it writes into the store, what moves and what stays, and the lean pass |
| **phase 4** | `${CLAUDE_PLUGIN_ROOT}/skills/adopt/references/reporting.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md` and `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`): the report's five parts, and the proposal grouped by category |

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
