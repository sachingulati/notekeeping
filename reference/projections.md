---
doc:   projections
title: The read line - what reaches a session, who writes it, and never clobbering what you did not create
---

# Projections

**A projection is the line that makes a session read its notes**, written into a file the harness
loads by itself. It is never a source, and it carries no content: the notes stay in the store, and
the line tells the session to read them. Losing every projection loses nothing - `/nk:doctor --fix`
rewrites them all.

**Every repo and workspace target is `CLAUDE.local.md`, and global is a rule file. Never write a file named `CLAUDE.md`, at any level.** That
file belongs to somebody at every level it appears - the team at a repo root, the user everywhere
else - and `CLAUDE.local.md` is the platform's own slot for uncommitted personal content. **The one
command that may edit a `CLAUDE.md`** is `/nk:adopt`, which removes lines whose content it has
already written into the store, on the yes to the proposal that showed the lines - and only from a
file in no work tree, or one the repository ignores by its exact path.

## What goes in

| Target | Holds |
|---|---|
| **repo** - `<repo>/CLAUDE.local.md` | one line, between the markers: `Before answering anything or starting any task here, read <abs store path>/projects/<name>/NOTES.md.` |
| **workspace** - `<workspace-root>/CLAUDE.local.md` | one line, between the markers: `Before answering anything or starting any task here, read <abs store path>/NOTES.md.` |
| **global** - `~/.claude/rules/notekeeping.md` | the fixed line, then the imports - below |

**The path is absolute, with forward slashes** - `/abs/path/to/ws/.notekeeping/projects/app/NOTES.md`,
with the drive letter in front on Windows. **Nothing else goes between the markers.** **A target
that delivers two scopes carries both lines in its one block** - a workspace root that is itself a
registered repository, the monorepo below: the workspace's line first, then the project's. It is
still one `notes:begin` block per file. The content stays in the store; a repo's or the workspace's `NOTES.md`
reads on to that scope's `instructions.md` and `areas/INDEX.md` itself, by lines its definition
carries. Global imports its `instructions.md` directly, below. **The line does not change when the notes do**, so nothing rewrites it after registration
except a repair.

**Each scope is delivered once.** The repo line reads the project's `NOTES.md` and nothing else; the
workspace line, loaded by the same ancestor walk, reads the workspace's; global is a rule file every
session loads. Never point one scope's line at another scope's file.

`<workspace-root>` is **the directory holding that store's `.notekeeping/`** - the same walk that
resolved the store already produced it. It is never configured and never guessed.

## Global

**`~/.claude/rules/notekeeping.md` is the plugin's file, whole** - nobody else writes it, so it has
no markers and no header block; it is rewritten entire. It holds, in order:

```
Notes are kept with Notekeeping. When the user asks in plain words to save, resume, record something or look it up in the notes, use the matching /nk: command. Never write a notekeeping file directly when a command covers it - run the command.

@<abs home>/.notekeeping/NOTES.md
@<abs home>/.notekeeping/instructions.md
@<abs home>/.notekeeping/environment.md
```

**Each import is `@` and an absolute path with forward slashes** - the form that loads from a
user-scope rule with no approval. **Write all three imports, always** - whether
or not each file exists yet. A missing file's import is skipped silently and loads the moment the
file is created, so the rule file is written once, at registration, and never needs a save to touch
it. **The fixed line is there** so a request made without a command name still
reaches one - and reaches it *through* the command, because a note written by hand skips the
admission test, the entry format and the duplicate check the command applies.

**A `CLAUDE.local.md` in the home directory is not a target.** A file there is not the plugin's to
touch.

## Who writes it, and when

| Skill | When | What |
|---|---|---|
| **`/nk:init`** | **registration** - a project is added, the workspace store created, the global store created | that project's line, the workspace's line, or the global rule file. **This is what makes a scope deliver**; a registered project is delivering before any save has run |
| **`/nk:doctor --fix`** | a repair | every line or rule file that is missing or wrong, in one run. Without `--fix` it reports and writes nothing |

**Nothing else writes one.** `/nk:save`, `/nk:project`, `/nk:adopt` and `/nk:upgrade` write no
projection: the line points at the notes, and the notes are what they change.

## Before writing anything

1. **Registration is the switch.** A registered project is delivered, always; a repository nobody
   registered gets no line. **The line is also how a skill tells which project a folder is** - the
   store-resolution rule reads it before the registry - so it keeps working when the folder is
   renamed or moved, and the registry's `dirs:` records where it was written.
   **Never write one into a registered directory that no longer exists** - `Write` would create the
   folder; the store-resolution rule's *A folder the registry has lost* says what to say instead.
2. **The no-clobber check** on the target, below.
3. **The ignore step first**, for a target inside a git work tree, below.

## The no-clobber rule

**Check the target before every write.** There are three cases and no others:

| The target | Do |
|---|---|
| **does not exist** | Create it. The whole file is the plugin's; the markers are just a generation boundary |
| **exists and has the markers** | Render the block; if it differs from what is between the markers, rewrite the header and the block. Everything else is preserved verbatim |
| **exists without them** | **Append the block at the end.** Never modify a byte above it |

**Detection keys on the markers appearing anywhere in the file - never on the file starting with the
generation header.** Once a block has been appended, that header no longer sits at the top, so a
top-of-file test fails to recognise a block already written and appends a second one on every write.
Search the whole file for `notes:begin`; that, and only that, decides between case 2 and case 3.

**Render the block - a line for every scope this target delivers - compare it to what is between
the markers, and write only if they differ** -
unchanged, it writes nothing; a moved store or a renamed project lands.

**The third case appends rather than refusing, and the rule it protects is unchanged: never clobber
a file this plugin did not create.** Every byte above the block stays untouched, and the block
converts the file into an ordinary case-2 target from then on.

**The notice goes in the header comment, not the body.** The body is the read line and nothing else; the header is where
*do not edit* already lives and is what a person editing the file actually reads:

```
<!-- GENERATED by the Notekeeping plugin - reads <source> - <date>
     Do not edit or delete this block; edits are overwritten. To get it back after deleting
     it, run <remedy>. Everything above it is yours and is never modified. It is rewritten
     for as long as <lifetime>. -->
```

| Target | `<remedy>` | `<lifetime>` |
|---|---|---|
| a repo | `/nk:doctor --fix` | `this project is registered` |
| the workspace root | `/nk:doctor --fix` | `this workspace store exists` |

`<source>` is the store file the line points at. **A block carrying both lines** names both files
in `<source>`, workspace first, and joins the two rows' `<lifetime>` with *or*. **This is the header, stated once.** It is written
the same way in both targets, with the two slots
filled from the row for that target and nothing else changed. **The rewrite covers the header as well as the block** -
it carries the source and the date, so leaving it in place while the block changes underneath it
publishes a generation date that is no longer true. **Global has no header**: the rule file is the
plugin's whole.

**A repo whose file already existed without the markers (case 3) still gets the ignore step.** The
file now carries generated content, so it is ignored exactly as one created here - see below.

## The ignore step, and its ordering

**Add one line to `.git/info/exclude`. That is the whole ignore step - there is no `.gitignore`
line and no setting that adds one**. `.gitignore` is tracked and team-owned; the projection is not
the team's file. The exclude is local, untracked and immediate, which are the projected file's own
properties.

**The line is `/CLAUDE.local.md` for a projection at the repository root, or the path relative to
the repo root with a leading `/` for one below it** - a workspace root that sits under the repo root,
not at it. Match on that exact line before appending, so the same entry is never added twice.

**Locate the exclude file by reading:** `<repo>/.git/info/exclude`, or - where `.git` is a file -
`<gitdir>/info/exclude`, and where that directory holds a `commondir` file,
`<gitdir>/<commondir>/info/exclude`. No git is run.

**Read the file, and append only if the entry is absent.** That file is the user's own, it usually
already holds their local excludes and git's shipped comment header, and **a write that replaces it
destroys all of them**. Appending without the check is the other half of the same defect: the entry
accumulates a copy per write. One read, one match, at most one appended line.

**Ordering:** the `.git/info/exclude` entry **first**, and **then** write the file - so in the
ordinary case there is no moment when the file exists unignored.

**If the exclude entry cannot be written, write the projection anyway.** A write under `.git/` is
one a harness may ask about or refuse, and without the line the repository gets none of its notes -
the plugin does not work there. Refused, declined or failed, the answer is the same: write
`CLAUDE.local.md`, and **unless the repository root's `.gitignore` holds the line `/CLAUDE.local.md`
or `CLAUDE.local.md`, warn in the report** - the file is untracked and not ignored, so a
`git add -A` there would commit it, absolute path to your notes and all; name `/nk:doctor --fix` as
the retry for the exclude, or a `.gitignore` line as the user's own alternative. **The warning is
not optional**: it is what stands in for the ignore that did not land.

**The rule keys on the target, not on which projection it is.** Apply it whenever the directory
being written to is inside a git work tree; skip it otherwise. A workspace root is usually not a
repo and needs nothing. A workspace root that *is* a repo - a monorepo - gets exactly the same
treatment as any other repo: one exclude entry, `/CLAUDE.local.md`, whichever of its two lines is
written first.

## Failing

**Registration writes the store first and the line last.** A failed line is a **warning**, never a
reason to undo the registration: the line is derivable, and `/nk:doctor --fix` writes it.

**Name what was and was not written**, and never report a line that was not written.
