---
name: summary
description: Write the work item's summary - what it is, what was done and why, and what to check before it ships - as a story, in markdown or a page. Use when the user asks to write up or summarise a task or work item.
argument-hint: "[id] [--dry-run] [--md | --html]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

Write a work item's summary. Writes inside the store only.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs. **Then the schema release**: read each resolved store's `schema_version` and `Grep` the release in `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (*The schema release this plugin ships*). A store at it is not mentioned; one behind it is said in one line on every run, naming `/nk:upgrade`, and the run carries on; newer or absent follows that file's four answers.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

Then the item:
`[id]`, or the item resolved per `${CLAUDE_PLUGIN_ROOT}/reference/item.md` -
acting on one match and asking where there are several or none (`## Asking`).

Resolve the `summary.md` definition per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` - the
user's overlay wins over the shipped default - and honour its admission and exclusion tests. It owns
what a summary holds, its *In a minute* part, its story and its two formats; this file owns how one
is written.

## The reader

**Whoever needs to understand this work without having done it** - most often the person about to
push work they did not write by hand, and also a reviewer, someone picking it up months later, or
anyone it is passed to. Assume none of them remembers the work, the author included.

## The format

**The first answer wins:**

1. **This run's own choice** - `--md` or `--html`, or the user asking in plain words for this one
   (*as a page this time*). It leaves the setting as it is.
2. **The store's `summary_format`**, per `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`.
3. **Unset** - ask *Markdown / HTML*, per `## Asking`. Write the answer to the store's `config.md` as
   `summary_format: <md|html>`, and say in one line that `/nk:config set summary_format` changes it.

**A file of the other format left by an earlier run is not deleted** - name it in one line as older
than this summary.

## Reading the item

**The item's notes are the source**: its `requirements.md` with every amendment, `plan.md`,
`resume.md`, `session.md`, `test.md`, `test-manual.md` and `instructions.md` where each exists, and
the registers' entries it promoted - `Grep` its id in its projects' registers. **An image in its
`artifacts/` may illustrate the story; nothing else there is read.**

## Picking the commits

**The goal: the commits that are this item's work, and only those.** Its notes say what changed; its
commits show the code. Find them per `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`, *an
item's commits*, in each repository of its projects, and judge each one:

- **Its working period** - from its first `## session` date to its last. A commit outside it is not
  this item's, however late the summary is written.
- **Its branch and its merge**, where it has its own.
- **The message, and the files the commit touched**, against what the notes say changed.

**A commit's change is read with `git show`, and a file with `Read`** - git is the only command this
skill runs, per the repo-facts rule.

**Name the commits used, and in one line the nearby ones left out as other work**, so a wrong call is
visible and can be corrected. **Where the notes and the commits disagree** - a change the notes
describe that no commit makes, or a commit the notes never mention - **say so where the story checks
before pushing.** Git unavailable: say so in one line, and write from the notes alone.

## Writing it

**Write from the notes and the commits, in the chosen format**, per the definition. **Nothing the
notes and commits do not hold is stated as fact.**

- **`md`** - write `summary.md` in the item.
- **`html`** - write `artifacts/summary.html` in the item, and no other file in that folder. **One
  self-contained page**: its styles, scripts and drawings inline, nothing fetched, so it opens from
  disk. It reads in light and dark, uses the window's full width with a side menu, keeps running text
  to a readable measure, gives *In a minute* a copy control, and folds code until it is opened. Then
  tell the user to open it in their browser.

**The terminal gets the *In a minute* part and the file's path**, whichever the format.

**`--dry-run`** prints the *In a minute* part and the story's chapter outline, and writes nothing.

## Asking

**Every question this skill asks follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`** - `nk:summary
needs:` and the open questions.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:summary`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.
