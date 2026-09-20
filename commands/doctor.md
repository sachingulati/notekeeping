---
description: Check the store for what is broken, drifting, or worth doing. Reports; --fix repairs the unambiguous.
argument-hint: "[--fix [<text>]] [--baseline] [--page | --no-page] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git status:*), Bash(git log:*), Artifact
---

Check the store. **Reports by default.** `--fix` repairs only the unambiguous and `--baseline` records
the findings as acknowledged; nothing else writes.

**`--caller <name>`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` - the counts
go in the outcome line's detail, and the findings below are not printed.

Resolve the store first, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and check only
what is inside it. A store you found rather than resolved is the wrong store.

This command exists because **the failures it looks for are silent.**

| Severity | Means |
|---|---|
| **error** | broken now - something does not work |
| **warn** | drifting - works, but degrading |
| **info** | an opportunity - a proposal you may decline |

**`--fix` repairs a finding at any severity when the repair is unambiguous** - a projection to
rebuild, an ignore entry to add, an index row to regenerate. Anything needing a judgment call is
reported and left alone. **Severity is not the test**: it says how bad a finding is, and
repairability says how certain the fix is. A stale projection is a `warn` with an exact repair; a
store version newer than this plugin is an error with no safe repair at all.

**Four prohibitions bound it whatever else is said**, and none of them is a severity: **never
migrate a store**, **never lower a schema stamp**, **never merge tags**, **never remove a registry
entry.**

### `--fix <text>`

**The flag takes free text, and what the text can do is bounded by the report this run just
printed.** It narrows and it disambiguates:

- **Narrow** - *"only the projections"*, *"skip the ignore entries"*. It picks from the findings in
  front of it.
- **Disambiguate** - supply the judgement a repair was missing, which is what makes a finding that
  was *reported and left alone* repairable in this run.

**It cannot reach past the finding list.** A repair for something this run did not report is not
authorised by any wording, and neither is anything the four prohibitions forbid - text that asks for
one is refused by name, and the rest of the instruction is still honoured.

**The report is the contract**: everything this flag does is something the same run printed. **Say
which repairs were unambiguous on their own and which the text authorised**, as two groups, so the
user can see what their sentence actually bought.

## Errors

- The store resolves, exists, and is readable - naming the directories the walk actually covered.
- Each project resolves to a real directory.
- **Each workspace in global's `## Workspaces` registry has a store at that path** - probed, per
  `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. **Report a stale entry; never remove one, and
  `--fix` must not**: an absent path is as likely to be an unmounted volume as a deleted store, and
  the two cannot be told apart from here. A workspace store with **no** entry is also a finding - it
  was created before the registry existed, or by hand.
- the store's `index.md` matches the folders on disk - no orphans, no dangling entries.
- Every work folder has a `requirements.md` with parseable frontmatter.
- A file declaring `authority: derived` that carries no refresh recipe.
- **A projection that is missing, stale, or missing its block**, per
  `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`. Check both targets: `<repo>/CLAUDE.local.md`
  and `<workspace-root>/CLAUDE.local.md`. Every registered project is expected to have one, and a
  file present without a `notes:begin` marker is the same finding as an absent one - the notes are
  not delivered either way. **Name the remedy: `/nk:project <name>`, or `--fix`.** Do not say the
  next save will write it; a save maintains the active work item's project and nothing else.
- **An unfinished migration.** A file whose stamp is **below its own definition's `schema:`** while
  the store's `schema_version` already claims that release, or a store left mid-run. **Name
  `/nk:upgrade` and say that re-running resumes it**, with the outstanding count per definition.
  Found by resolving each definition and grepping the stamp - **never by reading the files** -
  `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`. **`--fix` does not finish it**: a migration
  takes a person's confirmation.
- **A migration step that declares neither `on write` nor `upgrade only`.** A command holding the
  file cannot tell whether finishing it is within its remit, so the file is stuck between the two
  paths. An overlay check too.
- **A definition whose `schema:` is above 1 with no `## Migration` section.** The files are known to
  be outstanding and there is no way to convert them. This is the version-shaped twin of the missing
  `## Exclusion` check below, and it is an overlay check too.
- **A file stamped above its definition's version.** It is **excluded from migration** by the only
  test there is, so it will never be converted again. Two causes and you do not guess between them:
  the plugin is older than the store, or the stamp was raised by hand. **Report both, and repair
  neither** - lowering a stamp schedules a rewrite of the user's own file, and `--fix` must not.
- **Store `schema_version` that is not the version this plugin ships.** Check the resolved store
  and global, and report each. **Older -> name `/nk:upgrade`. Newer -> name the plugin update.
  Absent -> report it as absent**, never as current. The version and the four answers are
  `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`; read the number there rather than assuming
  one. **`--fix` does not migrate** - a migration rewrites knowledge and takes a person's
  confirmation.
- **The store is not readable from where the projections point.** Every `Read on demand` line
  carries an absolute path above the repository root, and Claude Code reads outside the working
  directory only where `additionalDirectories` in `~/.claude/settings.json` allows it. **Test it by
  reading a file you know is there** - the resolved store's `config.md` - rather than by parsing
  settings. A refusal is this finding: the on-demand half of delivery is not arriving. Name the
  store root and `~/.notekeeping` as the entries to add.
- A user-defined file definition with no `## Exclusion` section.
- **An overlay definition that replaces the shipped one whole and omits a load-bearing field** -
  `schema`, `enabled`, `budget` or `env_axis`. A whole replacement stands alone, so an omitted field
  is **switched off**, not inherited: `schema` takes that file out of every migration, `enabled`
  takes the file out of the store. **Say which are missing and what each now means for that file**,
  and repair none of them - filling one in is the merge `resolution.md` forbids. **Name `extends:`
  as the remedy** where inheriting was what the user meant.
- **An overlay whose `extends:` does not resolve.** The only value is `shipped`, and it extends the
  shipped definition of the same `file` and `scope`. Anything else - another overlay, a definition
  at a different scope, a name that does not exist - is an error: name the file and resolve nothing.
  **`extends:` in a prompt overlay or a user-defined command is the same finding**; only a file
  definition extends.
- **A fragment naming a field the definition format does not have.** Its table is closed
  (`resolution.md`, *What a definition carries*), and a typo in a fragment is silent in a way a typo
  in a whole replacement is not: the misspelt field is ignored and the inherited value stands, so
  the override the user wrote does nothing. Name the field, and the one it was probably meant to be.
- A user command whose `writes:` declaration does not match what it touches, or that shadows a
  shipped command name.

The overlay checks resolve definitions through
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` so you are checking the definition this store
actually uses rather than the shipped default.

## Warnings

- A registered repo with no `.git/info/exclude` entry for its projection. **`.gitignore` is never a
  finding** - the plugin does not write it.
- `NOTES.md` missing for an active project, or over budget.
- A knowledge file past `staleness_warn_commits` or `staleness_warn_days` - **either trips it**.
  Resolve both per `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`; do not assume a threshold.
- **A memory store this project's directories resolve to that the last save did not drain.** The
  harness keys memory by a working-directory slug, so a save run from a subdirectory - or a project
  that has moved - drains one store while another holds items nothing has read. **Name the paths**;
  the remedy is a save run from the directory that slug belongs to.
- **`instructions.md` past its own budget at a projected scope.** Say it in those words rather than
  as the slice being over: the remedy is retiring an entry or moving a conditional one into its
  area, not trimming `NOTES.md`.
- **A scope whose `instructions.md` has content and whose projection carries no
  `## Standing instructions` block.** Found by grepping the heading in the target, never by reading
  it - a stale projection delivers the facts and silently drops what was to be obeyed, and the block
  looks complete either way. `/nk:project <name>` or `--fix` rebuilds it.
- **A `resume.md` whose `Covers` line is behind `session.md`.** Compare the last session it names
  against `grep -n '^## session ' session.md | tail -1`. Behind means the summary was not brought up
  to the record, and what a later session resumes from is missing whole sessions. **Two greps, no
  reading.**

- **A `resume.md` carried forward five saves running.** The `Covers` line says `re-derived` or
  `carried forward`; a run of the second is a summary that has been a compression of a compression
  five times over. It is a warning rather than an error - nothing is lost, `session.md` still holds
  the record - and the remedy is one `/nk:load --full` before the next save.

- **An area directory with no line in that scope's `NOTES.md`.** It renders as its slug alone, which
  is a guess about what the shelf holds - `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, *What
  the on-demand half renders*.
- Content *outside* the block at either projection target - hand-written, or another tool's - is
  **never a finding**. It is expected: the block is appended below it and nothing above it is
  touched.
- An unresolved contradiction whose recorded check has not been run.
- A `requirements.md` amendment whose `Invalidates` names files nobody has revisited.

## Info

- **The harness memory store growing unpromoted** - items surviving several saves without being
  filed. **Some of this is expected and is not a defect**: an item the drain could not place is
  reported and deliberately left where it is, and an instruction that holds everywhere belongs in
  the harness instructions file rather than in any store. Report the count and the oldest date.
- A register past its split threshold - propose `areas/<topic>/`.
- The same fact in two projects - a workspace promotion, or a contract, per `depends_on`.
- A workspace or global entry whose provenance names one project - a demotion candidate.
- A `decisions.md` entry whose `Would reopen if:` trigger has plausibly fired, and **an
  `instructions.md` entry whose `Retire when:` has** - one finding, because they are the same
  question asked of the two files that carry a trigger. **Report it; never retire one.** An
  instruction still in force is obeyed on every task, so removing one on inference is the class of
  act this design forbids everywhere else.
- **Near-duplicate tags in `index.md`** - two tag values differing only by case, punctuation or
  plural, or obvious synonyms for one thread (`a11y` and `accessibility`). **Report both spellings
  with their item counts; never merge them, and this is never an error.** `/nk:save` may mint a tag
  from the session, so a variant can enter the store without anyone typing it - this is the repair
  path for that. Merging is a hand edit: only the user knows which spelling they meant, and `--fix`
  must not touch a label they chose.
- A work-item tree nested more than two deep.


**The report can be a page.** Where there is enough to choose between, offer it at the very
end, per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md` - which owns the offer, the two
flags, and what the page may carry. **The terminal report is printed either way.**

## The baseline

`--baseline` records the current findings as acknowledged, so day-one noise does not drown day-two
findings. Write it to the store's `baseline.md`. Report new findings against it by default, and say how
many are baselined rather than hiding the count.

**Both numbers follow `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`**: the findings are one
numbered list, the count is its last number, and *new* plus *baselined* is printed as arithmetic that
reconciles to it. A baseline that quietly absorbs a finding is the failure this command exists to
prevent, happening inside it.

## Never

Report on anything outside the store **beyond the targets this command is given to check** - the
two projection files, the ignore entry, a registered project's directory, and a registry path being
probed. Everything else outside `.notekeeping/` belongs to the user and will outlive this plugin;
loose files, scratch, and anything the user put there are theirs, and none of them is a finding.
