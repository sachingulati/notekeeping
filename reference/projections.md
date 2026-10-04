---
doc:   projections
title: The read line - what reaches a session, and who writes it
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

**Writing one** - the order, the no-clobber rule, the ignore step and failing - is the projection-writing rule; only the skills that write a line read it.
