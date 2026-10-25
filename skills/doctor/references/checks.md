# Doctor - the checks

**Read this before the first check.** Every check, by severity, with its key - the first part of a
finding's fingerprint. A rule a check names is listed with its file in the skill's *The checks*.

## Errors

- `store-unresolved` · The store resolves, exists, and is readable - naming the directories the walk actually covered.
- `folder-missing` · **Each registered folder is a real directory** - every `dirs:` line, a project with several
  checked once per folder. A registered directory that is gone, or that git
  says is not a repository, is reported, never removed - and `--fix` writes nothing into it, per
  the projection-writing rule. Where this run stands in a folder whose read line
  names that project, say so and name `/nk:init` here, per the resolution cases,
  *A folder the registry has lost*.
- `read-line-unfollowed` · **A read line resolution would not follow** - one naming another store, or a project the store
  does not hold - on the path from the working directory up, per the store-resolution rule,
  *Resolving a project inside the store*. Name the file and the line.
- `workspace-stale` · **Each workspace in global's `## Workspaces` registry has a store at that path** - probed, per
  the resolution cases, *Knowing that other stores exist*. **Report a stale entry; never remove one, and
  `--fix` must not**: an absent path is as likely to be an unmounted volume as a deleted store, and
  the two cannot be told apart from here. A workspace store with no entry is also a finding,
  filed as an error - `workspace-unregistered`.
- `project-shadowed` · **A project shadowed by a nearer workspace** - for each enclosing workspace the walk named
  (the store-resolution rule), a project in its `## Projects` whose folder lies
  inside a nearer workspace. Sessions there resolve to the nearer store, which does not hold it. Name
  the project, its folder and both stores. **`--fix` writes nothing**: moving a project between stores
  is the user's, by hand.
- `index-drift` · the store's `index.md` matches the folders on disk - no orphans, no dangling entries.
- `requirements-missing` · Every work folder has a `requirements.md` with parseable frontmatter.
- `derived-no-recipe` · A file declaring `authority: derived` that carries no refresh recipe.
- `read-line-missing` · **A read line or the rule file that is missing or wrong**, per
  the projections rule. Check all three targets: each registered
  folder's `<repo>/CLAUDE.local.md` holds, between the markers, the line that reads that project's
  `NOTES.md`; `<workspace-root>/CLAUDE.local.md` holds the line that reads the workspace's
  `NOTES.md`, and each enclosing workspace's root holds its own - a workspace root that is also a registered repository holds both lines, in one block; and `~/.claude/rules/notekeeping.md` holds the fixed line and an import of each of
  global's `NOTES.md`, `instructions.md` and `environment.md`, whether or not the file exists yet. Found by grepping for
  the path, never by reading the file. A `CLAUDE.local.md` present without a `notes:begin` marker is
  the same finding as an absent one - the notes are not delivered either way. The rule file has no
  markers by design: check it by its content, never for a marker. The rule file is expected
  wherever `~/.notekeeping/` exists. Name the remedy: `/nk:doctor --fix`, which writes each
  one per the projection-writing rule - except into a registered directory that is gone, above - the ignore step first, the no-clobber check on every target. Do not
  say the next save will write one; a save writes none.
- `migration-unfinished` · **An unfinished migration.** A file whose stamp is below its own definition's `schema:` while
  the store's `schema_version` already claims that release, a gather step's source still present
  once it does, or a store left mid-run. Name
  `/nk:upgrade` and say that re-running resumes it, with the outstanding count per definition.
  Found by resolving each definition and grepping the stamp - never by reading the files -
  the migration rules (with the store walk rules). **`--fix` does not finish it**: a migration
  takes a person's confirmation.
- `stamp-above` · **A file stamped above its definition's version.** It is excluded from migration by the only
  test there is, so it will never be converted again. Two causes and you do not guess between them:
  the plugin is older than the store, or the stamp was raised by hand. **Report both, and repair
  neither** - lowering a stamp schedules a rewrite of the user's own file, and `--fix` must not.
- `schema-version` · **Store `schema_version` that is not the version this plugin ships.** Check each resolved store,
  and report each. Older -> name `/nk:upgrade`. Newer -> name the plugin update.
  Absent -> report it as absent, never as current. The version and the four answers are
  the schema-version rule; read the number there rather than assuming
  one. **`--fix` does not migrate** - a migration rewrites knowledge and takes a person's
  confirmation.
- `store-unreadable` · **The store is not readable from where the read lines point.** Every read line
  carries an absolute path above the repository root, and Claude Code reads outside the working
  directory only where `additionalDirectories` in `~/.claude/settings.json` allows it. Test it by
  reading a file you know is there - the resolved store's `config.md` and `~/.notekeeping/config.md`
  - rather than by parsing settings; where the working directory is under the root being tested, the
  read proves nothing, so check the settings entry for that root instead. A refusal is this finding:
  the read line points at notes the session cannot read. The repair adds each missing root, under the store-writes rule's *The read permission*.
- **The overlay checks**, where step 2 found an overlay - each keyed `overlay-*` - every overlay definition, per
  `overlay-checks.md`.

- `secrets-committed` · **A `secrets.md` that would be committed** - inside a repository, git's *ignored* says no; outside
  one, or git unavailable, no `.gitignore` beside it names it, since a store put under git later would
  commit every secret in it. `--fix` adds the line, to a `.gitignore` that exists or a new one.
  **Check it by the file's path; never read `secrets.md`'s content**, here or anywhere in this
  run, and never quote it.

## Warnings

- `read-line-unignored` · A registered folder whose read line git does not ignore - the repo-facts rule's *ignored* on its
  `CLAUDE.local.md`; `--fix` appends the entry as
  the projection-writing rule's ignore step does. Say why it matters: the read line is there and unignored, so a
  `git add -A` would commit it. The repository's `.gitignore` is never a finding - the plugin writes none there.
- `agents-md-hidden` · **A registered folder whose `AGENTS.md` its read line now hides.** Claude Code
  reads a repository's `AGENTS.md` only when no `CLAUDE.md`, `.claude/CLAUDE.md` or `CLAUDE.local.md`
  sits in the working directory or above it, and the read line is a `CLAUDE.local.md` - so a folder
  that relied on `AGENTS.md` stops loading it once it is registered. For each registered folder,
  `Read` `<dir>/AGENTS.md`; where it exists it still loads if `<dir>/CLAUDE.md` or
  `<dir>/.claude/CLAUDE.md` imports it - `Grep` `@AGENTS.md` - or `~/.claude/settings.json` holds
  `claude-md-and-agents-md`, the project-instructions value that reads both. Otherwise report it,
  with the two ways back, both the user's: a line `@AGENTS.md` above the block in that
  `CLAUDE.local.md`, or **Project instructions** set to `claude-md-and-agents-md` in `/config`.
  **`--fix` writes neither** - the line belongs to the user's part of the file, and the setting is
  theirs.
- `config-unknown-key` · **A key in a store's `config.md` that the config defaults do not
  list** - a retired setting or a typo. It does nothing,
  silently. Name the key and the nearest known one; never remove it or rename it, and `--fix`
  must not - which key was meant is the user's call. `schema_version` and the `## Projects` and
  `## Workspaces` registry rows are known.
- `project-lines-two` · **Two project lines on the path** from the working directory up - a block in a subfolder as well
  as at the repository root - so a session there reads both projects' notes. Name both files; the
  nearer resolves, per the resolution cases, *Two project lines on the path*. Repair neither.
- `notes-missing`, `notes-over-budget` · `NOTES.md` missing for a registered project or for the workspace, or over budget. **A byte budget is
  checked against the file-facts rule's *size***,
  every budgeted file in one batch; an estimate where it cannot be had, said so.
- `stale-stamp` · A knowledge file past `staleness_warn_days`, on the repo or the env axis - days since the stamp
  are the only trip, per the staleness rule (with the repo-facts rule). *HEAD moved* is never a
  warning.
  Resolve it per the config defaults; do not assume a threshold.
- `memory-undrained` · **A memory store this project's directories resolve to that still holds items** - an empty file
  is an item already cleared, not one - per the drain rule, which also says why a save run
  from a subdirectory - or a project that has moved - drains one store while another accumulates
  items nothing has read. Name the paths; the remedy is a save run from the directory that slug
  belongs to.
- `instructions-over-budget` · **`instructions.md` past its own budget.** Say it in those words rather than
  as the slice being over: the remedy is retiring an entry or moving a conditional one into its
  area, not trimming `NOTES.md`.
- `harness-duplicate`, `harness-contradiction` · **A global instruction that duplicates or contradicts your harness instructions file**
  (`~/.claude/CLAUDE.md`). The harness file and the global rule file that imports
  `~/.notekeeping/instructions.md` both load in every session. A duplicate is what a move
  that was never trimmed leaves behind - the copy in `~/.notekeeping/instructions.md` is the one to
  keep, and the remedy is applying that adopt run's trim items by number
  (`/nk:adopt --apply <run> <numbers>`), or removing the lines by hand. A contradiction is two
  rules and no way to tell which wins. Report either as the pair, quoting each; **never edit either,
  and `--fix` must not** - which one is right is the user's call.
- `resume-missing` · **An item with five or more saving sessions and no `resume.md`** - the count and threshold are
  `work/resume.md`'s, *When it starts*. A warning: the record is whole, and the next `/nk:save` writes
  it. **An item under five with none is the shape, never a finding.**
- `resume-behind` · **A `resume.md` whose `Covers` line is behind `session.md`** - never a run item's, which has neither. Compare the last session it names
  against the latest block in `session.md` - `Grep` `^## session ` with line numbers and take the last match. Behind means the summary was not brought up
  to the record, and what a later session resumes from is missing whole sessions. Two greps, no
  reading.

- `resume-carried-forward` · **A `resume.md` carried forward for five sessions** - `Covers` says `carried forward since <id>`,
  and five distinct session ids follow `<id>` in `session.md`, counted per `work/resume.md`'s rule 1 -
  one grep, no reading. The summary has been extended from itself five times over. A warning, since
  `session.md` still holds the record; the remedy is one `/nk:load --full` before the next save. A
  `Covers` line with no id is not counted - the next save writes one.

- `pending-old` · **A `pending.md` holding a line set aside more than `staleness_warn_days` ago** - facts a
  `/nk:save --no-promote` wrote down that no save has promoted since, per the pending rule. `Grep`
  `^- ` with `glob` `pending.md` under `<store>/work`, with line content; the date leads each line.
  Name the item, the count and the oldest date, and say which lines are `held` and why. The remedy
  is a save without `--no-promote`, or answering what held them; **`--fix` must not promote**.

- `area-unindexed` · **An area directory with no row in that scope's `areas/INDEX.md`.** No key sends a session into
  it, so it is never read.
- `secrets-unlisted` · **A `secrets.md` its scope's `NOTES.md` does not list under `## Read on demand`** - a session
  would ask the user for a login it already has. `--fix` adds the line, per `secrets.md`'s definition.
- `notes-line-missing` · **A read line missing from `NOTES.md`**: the scope has `instructions.md` or `areas/INDEX.md` and its
  `NOTES.md` carries no line reading it (global: no area lines, where `areas/INDEX.md` exists) - per
  that scope's `NOTES.md` definition. Nothing points a session at the file, so it is never read.
  `--fix` adds the line.
- Content *outside* the block at any read-line target - hand-written, or another tool's - is
  never a finding. It is expected: the block is appended below it and nothing above it is
  touched.
- `unsettled` · **An `Unsettled` note** on any entry - a contradiction whose check has not been run
  (the form in the promotion rule). Quote the check; never run it here.
- `run-interrupted` · **A run item whose `log.md` has a `start` with no `done`** - an apply that
  stopped part-way. Name the run and the items; the remedy is `/nk:<command> --apply <run>`, which
  checks each before writing. Doctor never finishes it.

## Info

- `workspace-depth` · **More than two workspace levels** - the walk named two or more enclosing workspaces. Allowed;
  usually a sign that one of them wants to be a project or nothing.
- `memory-old` · **The harness memory store holding old, unfiled items.** Some of this is expected and is not a
  defect: an item the drain could not place is reported and deliberately left where it is. Report
  the count and the oldest date.
- `register-split`, `fact-in-two-projects`, `wide-names-one-project` · **The content tests, as far as structure shows them**: a register
  past its split threshold (propose `areas/<topic>/`), the same fact in two projects (a workspace
  promotion), a workspace or global entry whose text names one project (a demotion candidate).
- `trigger-fired` · A `decisions.md` entry whose `Would reopen if:` trigger has plausibly fired (same file), and an
  `instructions.md` entry whose `Retire when:` has - one finding, because they are the same
  question asked of the two files that carry a trigger. An instruction with no `Retire when:` is a
  standing one and never a finding. **Report it; never retire one.** An
  instruction still in force is obeyed on every task, so removing one on inference is the class of
  act this design forbids everywhere else.
- `tags-near-duplicate` · **Near-duplicate tags in `index.md`** - two tag values differing only by case, punctuation or
  plural, or obvious synonyms for one thread (`a11y` and `accessibility`). **Tags starting `run-` are
  reserved and never paired** - `run-adopt` beside `adopt` is not a near-duplicate. **Report both spellings
  with their item counts; never merge them, and this is never an error.** `/nk:save` may mint a tag
  from the session, so a variant can enter the store without anyone typing it - this is the repair
  path for that. Merging is a hand edit: only the user knows which spelling they meant, and `--fix`
  must not touch a label they chose.
- `tree-deep` · A work-item tree nested more than two deep.
- `run-unfinished` · **A run item with `drafts/` and no `report.md`** - a pass that stopped, per
  the run engine, *Resuming*. Name the run and its command; the
  remedy is that command, bare, which offers to resume it. **Never delete a run item or its drafts.**
