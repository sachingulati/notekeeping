---
name: doctor
description: Check the Notekeeping setup and store for what is broken, drifting or worth doing, and repair the unambiguous with --fix. Use when the user asks to check, diagnose or repair the notes setup.
argument-hint: "[--fix [<text>]] [--baseline] [--page | --no-page] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact
---

Check the store. Reports by default, saves the report, then asks once whether to repair what is
unambiguous - *Apply / Apply and publish / Change answers / Not now*. `--fix` repairs only the
unambiguous, in the same run, and `--baseline` records the findings as acknowledged; nothing else
writes to the store. Every run saves its report to `.notekeeping/tmp/`, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s *The report is saved* - output, not a store write.
**This skill runs no shell and no git**: what it needs from a repository it reads from `.git` with
the file tools.

Before anything else, in order:
1. **`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
2. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`.
3. **An overlay?** If `<store>/schema/skills/doctor/` exists, follow `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`: a `SKILL.md` there replaces the rest of this file, and a file under its `references/` replaces the shipped reference of that name wherever this skill cites it.
4. **Infer before asking**: read what the conversation already states, and ask only what is still open.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:doctor`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

**A re-entry? Decide before any check.** Where the conversation holds this skill's report question
with an answer typed after it, and the report that question named has no `## Applied`, **no check
runs**: read that report, append the answer as `## Answers` keyed by finding number, show each
finding's resolved repair, and ask the report question again - the report shape's rules, with the
findings as the items. Otherwise the run is fresh.

Under `--oneline` the counts
go in the outcome line's detail, and the findings go to the report file it names.

Check only
what is inside the resolved store. A store you found rather than resolved is the wrong store.

| Severity | Means |
|---|---|
| **error** | broken now - something does not work |
| **warn** | drifting - works, but degrading |
| **info** | an opportunity - a proposal you may decline |

`--fix` repairs a finding at any severity when the repair is unambiguous - a read line or the rule file
to write, an ignore entry to add, or an index row to regenerate in the shape
`${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` states, which every writer of that file follows.
Anything needing a judgment call is reported and left alone. Severity is not the test: it says
how bad a finding is, and
repairability says how certain the fix is. A missing ignore entry is a `warn` with an exact repair; a
store version newer than this plugin is an error with no safe repair at all.

Four prohibitions bound it whatever else is said, and none of them is a severity: **never
migrate a store**, **never lower a schema stamp**, **never merge tags**, **never remove a registry
entry.**

### The report question

Where the saved report holds a repairable finding, end with the report question -
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s `## A yes applies what was shown` - so list each
repair as what it will write, not only as what is wrong. It is asked per
`${CLAUDE_PLUGIN_ROOT}/reference/asking.md`, with
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` when nobody can answer. There is no
`--apply`:

| Pick | Do |
|---|---|
| **Apply** | the in-run repair over exactly the report's repairable findings, as its `## Answers` leave them - read from the report, never the conversation; each finding re-checked before its write; `## Applied` appended |
| **Apply and publish** | *Apply*, then the page, per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md` |
| **Change answers** | ask again, the current answers shown |
| **Not now** | the report stays, and nothing applies it later: a later session runs `--fix` fresh |

An answer in words narrows or disambiguates, as `--fix <text>` does - so it can make a finding
that was *reported and left alone* repairable, and it is bounded exactly as that flag is, below.
No repairable finding, or no turn to answer in: no question - the report is the output, and the
page is offered as a line of its own where `report-pages.md` says there is enough to choose between.

### `--fix <text>`

The flag takes free text, and what the text can do is bounded by the report this run just
printed. It narrows and it disambiguates:

- **Narrow** - *"only the read lines"*, *"skip the ignore entries"*. It picks from the findings in
  front of it.
- **Disambiguate** - supply the judgement a repair was missing, which is what makes a finding that
  was *reported and left alone* repairable in this run.

It cannot reach past the finding list. A repair for something this run did not report is not
authorised by any wording, and neither is anything the four prohibitions forbid - text that asks for
one is refused by name, and the rest of the instruction is still honoured.

The report is the contract: everything this flag does is something the same run printed. Say
which repairs were unambiguous on their own and which the text authorised, as two groups, so the
user can see what their sentence actually bought.

## Errors

- The store resolves, exists, and is readable - naming the directories the walk actually covered.
- **Each project resolves to a real directory.** A registered directory that holds no `.git` any
  more is reported, never removed - and `--fix` writes nothing into it, per
  `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`. Where this run stands in a folder whose read line
  names that project, say so and name `/nk:init` here, per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`,
  *A folder the registry has lost*.
- **A read line resolution would not follow** - one naming another store, or a project the store
  does not hold - on the path from the working directory up, per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`,
  *Resolving a project inside the store*. Name the file and the line.
- **Each workspace in global's `## Workspaces` registry has a store at that path** - probed, per
  `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`. **Report a stale entry; never remove one, and
  `--fix` must not**: an absent path is as likely to be an unmounted volume as a deleted store, and
  the two cannot be told apart from here. A workspace store with no entry is also a finding,
  filed as an error.
- the store's `index.md` matches the folders on disk - no orphans, no dangling entries.
- Every work folder has a `requirements.md` with parseable frontmatter.
- A file declaring `authority: derived` that carries no refresh recipe.
- **A read line or the rule file that is missing or wrong**, per
  `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`. Check all three targets: each registered
  project's `<repo>/CLAUDE.local.md` holds, between the markers, the line that reads that project's
  `NOTES.md`; `<workspace-root>/CLAUDE.local.md` holds the line that reads the workspace's
  `NOTES.md` - a workspace root that is also a registered repository holds both lines, in one block; and `~/.claude/rules/notekeeping.md` holds the fixed line and an import of each of
  global's `NOTES.md`, `instructions.md` and `environment.md`, whether or not the file exists yet. Found by grepping for
  the path, never by reading the file. A `CLAUDE.local.md` present without a `notes:begin` marker is
  the same finding as an absent one - the notes are not delivered either way. The rule file has no
  markers by design: check it by its content, never for a marker. The rule file is expected
  wherever `~/.notekeeping/` exists. Name the remedy: `/nk:doctor --fix`, which writes each
  one per `projections.md` - except into a registered directory that is gone, above - the ignore step first, the no-clobber check on every target. Do not
  say the next save will write one; a save writes none.
- **An unfinished migration.** A file whose stamp is below its own definition's `schema:` while
  the store's `schema_version` already claims that release, or a store left mid-run. Name
  `/nk:upgrade` and say that re-running resumes it, with the outstanding count per definition.
  Found by resolving each definition and grepping the stamp - never by reading the files -
  `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`). **`--fix` does not finish it**: a migration
  takes a person's confirmation.
- **A migration step that declares neither `on write` nor `upgrade only`.** A command holding the
  file cannot tell whether finishing it is within its remit, so the file is stuck between the two
  paths. An overlay check too.
- **A definition whose `schema:` is above 1 with no `## Migration` section.** The files are known to
  be outstanding and there is no way to convert them. This is the version-shaped twin of the missing
  `## Exclusion` check below, and it is an overlay check too.
- **A file stamped above its definition's version.** It is excluded from migration by the only
  test there is, so it will never be converted again. Two causes and you do not guess between them:
  the plugin is older than the store, or the stamp was raised by hand. **Report both, and repair
  neither** - lowering a stamp schedules a rewrite of the user's own file, and `--fix` must not.
- **Store `schema_version` that is not the version this plugin ships.** Check the resolved store
  and global, and report each. Older -> name `/nk:upgrade`. Newer -> name the plugin update.
  Absent -> report it as absent, never as current. The version and the four answers are
  `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`; read the number there rather than assuming
  one. **`--fix` does not migrate** - a migration rewrites knowledge and takes a person's
  confirmation.
- **The store is not readable from where the read lines point.** Every read line
  carries an absolute path above the repository root, and Claude Code reads outside the working
  directory only where `additionalDirectories` in `~/.claude/settings.json` allows it. Test it by
  reading a file you know is there - the resolved store's `config.md` and `~/.notekeeping/config.md`
  - rather than by parsing settings; where the working directory is under the root being tested, the
  read proves nothing, so check the settings entry for that root instead. A refusal is this finding:
  the read line points at notes the session cannot read. The repair adds each missing root, under the rule in `${CLAUDE_PLUGIN_ROOT}/reference/store/writes.md`, *The read permission*.
- A user-defined file definition with no `## Exclusion` section.
- **An overlay definition that replaces the shipped one whole and omits a load-bearing field** -
  `schema`, `enabled` or `budget`. A whole replacement stands alone, so an omitted field
  is switched off, not inherited: `schema` takes that file out of every migration, `enabled`
  takes the file out of the store. Say which are missing and what each now means for that file,
  and repair none of them - filling one in is the merge `resolution.md` forbids. Name `extends:`
  as the remedy where inheriting was what the user meant.
- **An overlay whose `extends:` does not resolve.** The only value is `shipped`, and it extends the
  shipped definition of the same `file` and `scope`. Anything else - another overlay, a definition
  at a different scope, a name that does not exist - is an error: name the file and resolve nothing.
  `extends:` in a skill overlay is the same finding; only a file definition extends.
- **A fragment naming a field the definition format does not have.** Its table is closed
  (`resolution.md`, *What a definition carries*), and a typo in a fragment is silent in a way a typo
  in a whole replacement is not: the misspelt field is ignored and the inherited value stands, so
  the override the user wrote does nothing. Name the field, and the one it was probably meant to be.

The overlay checks resolve definitions through
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, and skill overlays per `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`, so you are checking the definition this store
actually uses rather than the shipped default.

## Warnings

- A registered repo with no `.git/info/exclude` entry for its read line - the exclude file located
  and the entry matched as `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`'s ignore step does,
  through `gitdir:` and `commondir` - unless the repository root's `.gitignore` already ignores the
  file, per the same rule. Say why it matters: the read line is there and unignored, so a
  `git add -A` would commit it. `.gitignore` is never a finding - the plugin does not write it.
- **A key in a store's `config.md` that `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md` does not
  list** - a retired setting or a typo. It does nothing,
  silently. Name the key and the nearest known one; never remove it or rename it, and `--fix`
  must not - which key was meant is the user's call. `schema_version` and the `## Projects` registry
  rows are known.
- **Two project lines on the path** from the working directory up - a block in a subfolder as well
  as at the repository root - so a session there reads both projects' notes. Name both files; the
  nearer resolves, per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`. Repair neither.
- `NOTES.md` missing for an active project or for the workspace, or over budget.
- A knowledge file past `staleness_warn_days`, on the repo or the env axis - days since the stamp
  are the only trip, per `${CLAUDE_PLUGIN_ROOT}/reference/staleness.md`. *HEAD moved* is never a
  warning.
  Resolve it per `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`; do not assume a threshold.
- **A memory store this project's directories resolve to that still holds items** - an empty file
  is an item already cleared, not one - per `${CLAUDE_PLUGIN_ROOT}/reference/drain.md`, which also says why a save run
  from a subdirectory - or a project that has moved - drains one store while another accumulates
  items nothing has read. Name the paths; the remedy is a save run from the directory that slug
  belongs to.
- **`instructions.md` past its own budget.** Say it in those words rather than
  as the slice being over: the remedy is retiring an entry or moving a conditional one into its
  area, not trimming `NOTES.md`.
- **A global instruction that duplicates or contradicts your harness instructions file**
  (`~/.claude/CLAUDE.md`). The harness file and the global rule file that imports
  `~/.notekeeping/instructions.md` both load in every session. A duplicate is what a move
  that was never trimmed leaves behind - the copy in `~/.notekeeping/instructions.md` is the one to
  keep, and the remedy is applying the saved adopt report's trim items by number
  (`/nk:adopt --apply <report> <numbers>`), or removing the lines by hand. A contradiction is two
  rules and no way to tell which wins. Report either as the pair, quoting each; **never edit either,
  and `--fix` must not** - which one is right is the user's call.
- **A `resume.md` whose `Covers` line is behind `session.md`.** Compare the last session it names
  against the latest block in `session.md` - `Grep` `^## session ` with line numbers and take the last match. Behind means the summary was not brought up
  to the record, and what a later session resumes from is missing whole sessions. Two greps, no
  reading.

- **A `resume.md` carried forward five saves running** - the threshold `work/resume.md` sets. The `Covers` line says `re-derived` or
  `carried forward`; a run of the second is a summary that has been a compression of a compression
  five times over. It is a warning rather than an error - nothing is lost, `session.md` still holds
  the record - and the remedy is one `/nk:load --full` before the next save.

- **An area directory with no row in that scope's `areas/INDEX.md`.** No key sends a session into
  it, so it is never read.
- **A read line missing from `NOTES.md`**: the scope has `instructions.md` or `areas/INDEX.md` and its
  `NOTES.md` carries no line reading it (global: no area lines, where `areas/INDEX.md` exists) - per
  that scope's `NOTES.md` definition. Nothing points a session at the file, so it is never read.
  `--fix` adds the line.
- Content *outside* the block at any read-line target - hand-written, or another tool's - is
  never a finding. It is expected: the block is appended below it and nothing above it is
  touched.
- **An `Unsettled` note** on any entry - a contradiction whose check has not been run
  (the form in `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`). Quote the check; never run it here.

## Info

- **The harness memory store holding old, unfiled items.** Some of this is expected and is not a
  defect: an item the drain could not place is reported and deliberately left where it is. Report
  the count and the oldest date.
- **The content tests, as far as structure shows them** (`${CLAUDE_PLUGIN_ROOT}/reference/content-tests.md`): a register
  past its split threshold (propose `areas/<topic>/`), the same fact in two projects (a workspace
  promotion), a workspace or global entry whose provenance names one project (a demotion candidate).
- A `decisions.md` entry whose `Would reopen if:` trigger has plausibly fired (same file), and an
  `instructions.md` entry whose `Retire when:` has - one finding, because they are the same
  question asked of the two files that carry a trigger. An instruction with no `Retire when:` is a
  standing one and never a finding. **Report it; never retire one.** An
  instruction still in force is obeyed on every task, so removing one on inference is the class of
  act this design forbids everywhere else.
- **Near-duplicate tags in `index.md`** - two tag values differing only by case, punctuation or
  plural, or obvious synonyms for one thread (`a11y` and `accessibility`). **Report both spellings
  with their item counts; never merge them, and this is never an error.** `/nk:save` may mint a tag
  from the session, so a variant can enter the store without anyone typing it - this is the repair
  path for that. Merging is a hand edit: only the user knows which spelling they meant, and `--fix`
  must not touch a label they chose.
- A work-item tree nested more than two deep.
- **Entries still marked `unseen`** - promoted in a `--oneline` run, so no person has read them
  (`${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`). Count them per scope and name the files.
  **Never clear the mark**: reading the entry is what clears it, and that is a person's hand edit.
- **Saved reports in `.notekeeping/tmp/` that have been applied, or are more than a week old**
  (`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`, *The report is saved*). **`--fix` writes
  each one empty** - nothing this command is granted removes a file - never one from today, which
  someone may still be reading or about to apply, and leaves `tmp/.gitignore`. An empty report is
  a removed one: never count it here. The files themselves the user may delete by hand. **The
  sweep never reaches `tmp/adopt-*/`** - those are adopt's trim copies, below.
- **Adopt's trim copies** - one folder per adopt run under `.notekeeping/tmp/adopt-*/`, each holding
  the files that run trimmed, whole, so a trim can be reverted by copying one back. Report how many
  runs' copies exist, found by `Glob` on `tmp/adopt-*/**` and counted by folder - a glob never
  returns a folder itself. **Never empty or delete one, and `--fix` must not**: they are the only
  way back from a trim.

The report can be a page - the report question's *Apply and publish*, or the line of its own
where there is no question - per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`, which owns the
offer, the two flags, and what the page may carry. The terminal report is printed either way.

## The baseline

`--baseline` records the current findings as acknowledged, so day-one noise does not drown day-two
findings. Write it to the store's `baseline.md`. Report new findings against it by default, and say how
many are baselined rather than hiding the count.

Both numbers follow `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`: the findings are one
numbered list, the count is its last number, and *new* plus *baselined* is printed as arithmetic that
reconciles to it. A baseline that quietly absorbs a finding is the failure this command exists to
prevent, happening inside it.

## Never

**Report on nothing outside the store beyond the targets this command is given to check.** Those
targets: the three read-line targets, the `CLAUDE.local.md` files project resolution reads on the
path from the working directory up, the ignore entry, a registered project's directory, a registry
path being probed, the harness memory stores this project's directories resolve to, the harness
instructions file (read for the contradiction check and never written),
`~/.claude/settings.json` (read and added to by the read-permission repair), and **a registered
repository's `.git`, read and never written** - `HEAD`, the ref it names or `packed-refs` for
staleness, and `info/exclude` (with `gitdir:` and `commondir`) for the ignore entry, which the
ignore-entry repair appends to.

**Everything else outside `.notekeeping/` belongs to the user.** Loose files, scratch, and anything
else the user put there are theirs, and none of them is a finding.
