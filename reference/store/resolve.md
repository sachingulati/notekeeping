---
doc:   store-resolve
title: Resolving a store, and the project inside it
---

# The store boundary

**Resolve the store first, before reading or writing anything. Every read and every write stays
inside it.**

A **store** is a `.notekeeping/` directory. There are two kinds and no others:

| Store | Where | Its scope |
|---|---|---|
| **global** | `~/.notekeeping/` - a fixed path, always | your working life, not any codebase |
| **workspace** | `<workspace>/.notekeeping/` | the projects that sit in that workspace |

**A store's own scope sits at its root; every narrower scope is a subdirectory.** So a workspace
store holds `projects/<name>/` and `work/<bucket>/<id>/` beneath it, and its own files - `NOTES.md`,
`instructions.md`, `domain.md`, `gotchas.md`, `decisions.md`, `process.md` and `areas.md` - sit
directly in `.notekeeping/`. There is no `global/` directory: `~/.notekeeping/` **is** global scope.

**`config.md`, `index.md` and `baseline.md` at a store's root are the plugin's own bookkeeping rather
than knowledge** - each has a definition of shape `bookkeeping`, so a release can migrate it like any
other file, and nothing that sweeps the store for knowledge files treats one as knowledge. **`tmp/`**
holds copies and saved reports, and no definition governs it.

## Resolving it

1. **Walk up** from the working directory to the nearest `.notekeeping/`. That is the workspace
   store. **Test the exact path at each level** - does `<dir>/.notekeeping` exist - rather than
   listing a directory and reading what comes back. **A store is a hidden directory, and a listing
   is not a reliable witness to one:** a plain `ls` omits it, and a glob of `*` has both skipped
   hidden directories and descended into them, depending on the harness. A walk built on a listing
   can report *no store* while standing next to one.

   **The failure is silent, and so is the obvious fix.** Probe for the store **by name**; never
   ask a listing to reveal it:

   | Pattern | Finds a `.notekeeping/`? |
   |---|---|
   | `<dir>/.notekeeping/*` | **yes** - the literal name is in the pattern. **Use this one** |
   | `<dir>/**/<file>` | **yes** - a recursive descent crosses into hidden directories |
   | `<dir>/*` | **not dependably.** It has behaved both ways - and where it descends, it returns files from any depth, so a hit does not say which level holds the store |
   | `<dir>/.*` | **no.** The intuitive correction matches hidden files at that level, not what is inside a hidden directory, and reports nothing rather than erroring |
   | `<dir>/*/<file>` | **no - it can return nothing with the file present.** See *A pattern that matches nothing* |

   **So: `Glob` the exact path `<dir>/.notekeeping/*`, or `Read` a file you expect inside it.**
   Existence is proved by a hit, never by a name's absence from a listing. **A walk that returns
   "no store" after only listing directories has not looked**, and must not be reported as an
   absence.

   **The walk stops below `~`.** Under your home directory, the last level probed is the one
   directly beneath `~` - `~` itself is global's home, a workspace cannot sit there, and nothing
   above it is yours. Outside your home directory, walk to the filesystem root. **A probe that
   errors or times out is named as unprobed**, not counted as an absence and not reported as an
   outer store: say which levels could not be checked, and carry on with the nearest store found.
2. `~/.notekeeping/` is **global**, and is an ancestor of every workspace, so it is always in scope.
   **Prove it by `Read` of `~/.notekeeping/config.md`**, never by a `Glob` pattern holding `~` -
   see *A ~ path*.
3. **Found nothing?** Stop and say so. Point at `/nk:init`. Do not continue with a guess.
   **Name the directories you actually walked.** *"No store above here"* is a claim about every
   ancestor, and it is worth exactly as much as the walk behind it - so make the walk visible and
   the claim checkable. **Do not report an absence you did not look for.**

4. **Found more than one below `~/`?** Workspaces do not nest. **Use the nearest**, and say that the
   outer one exists and is being ignored, naming both paths. Do not refuse - running against the
   obviously-intended store and saying so beats not running at all. `/nk:doctor` reports it as an
   error.

**Walking up is resolution, not discovery, and the difference is what you match on.** You are
looking for one exact name that the plugin creates and nothing else does. A `.notekeeping/` exists
only because someone ran `/nk:init` there, so finding one above you is finding a declaration, not
guessing from shape.

## Never infer a store from the filesystem

**A directory that looks like a notes store is not a store.** Do not scan the home directory, and do
not adopt a folder because it contains markdown, a `Tickets/` directory, a `notes/` directory, or
files shaped like notes. **Only an exact `.notekeeping/` counts.**

**Resolving against the wrong pile is not a harmless error.** `load` only reads, but `save`
resolves a target the same way and `save` writes.

## Knowing that other stores exist

**Global's `config.md` carries a `## Workspaces` registry** - the absolute path of every workspace
root whose store `/nk:init` has created on this machine:

```
## Workspaces
- /abs/path/to/ws
```

A store config's own `## Projects` is the same shape, one line per registered project:

```
## Projects
- **repo-a** - dirs: /abs/path/to/ws/repo-a
```

Both are written at creation, for the same reason, and neither is **a setting**.

**It is for enumeration, and never for resolution.** *Which store am I in* is always the walk, on
every command, with no exception and no shortcut. The registry answers only *what else is there* -
naming a candidate a command may then offer to act on.

**A registry entry is a candidate, never a fact.** Before acting on one, prove the store is there the
way existence is always proved here: probe `<path>/.notekeeping/*` and take a hit. A directory can be
moved, deleted, renamed or sit on a volume that is not mounted, and **a path written down a year ago
is not evidence that anything is there now.**

**A stale entry is reported, never removed.** `/nk:doctor` names it; nothing deletes it, because an
absent path is as likely to be an unmounted disk as a deleted store, and the two are not
distinguishable from here.

## A ~ path

Pass a path under the home directory to `Read`, `Grep`, `Write` and `Edit` exactly as written - `~/.notekeeping/config.md`. Never expand `~` yourself: the home directory you would compute is a guess, and the tool resolves it correctly.

**`Glob` takes `~` in its `path`, never in its pattern.** A pattern of `~/.notekeeping/*` matches
nothing with global present - an empty answer that is not an absence. Glob `*` with `path`
`~/.notekeeping`, or `Read` a file you expect there.

**Where an absolute home path must be written** - a read line, an import, a settings entry - take it
from a tool's answer, never from a guess: the paths a `Glob` with `path` `~/.notekeeping` returns
begin with the absolute home. Write it with forward slashes.

## Resolving a project inside the store

**Never invent a project.** A project exists because `/nk:init` created it, and init leaves two
records of it: the read line in the folder's `CLAUDE.local.md`, and the folder's path in the store's
`## Projects`. **The read line comes first.** It is what the session was given, so resolving by it
works on the project whose notes the session is reading - and it moves with the folder, where the
path in the registry does not.

**Compare paths, never names.** Normalise both sides first - forward slashes, no trailing slash, the drive letter in lower case, and `/<letter>/` read as `<letter>:/` (`/c/` is `c:/`, `/d/` is `d:/`). **Match whole segments**: `/x/app` covers `/x/app/src`, never `/x/app2`. On Windows, compare without regard to case.

1. **The read line.** From the working directory up to and including the folder holding the store's
   `.notekeeping/`, `Read` `CLAUDE.local.md` at each level, and take the nearest whose `notes:begin`
   block holds a project line - `read <store>/projects/<name>/NOTES.md`, the projections rule's
   shape. A workspace line, `read <store>/NOTES.md`, names no project and is passed over. **Read the
   file**: that the harness loaded it into the session is not a reading of it. **Follow the line
   only when `<store>` is the store resolved above and `projects/<name>/` exists in it** - then
   `<name>` is the project. Otherwise do not follow it, and say why:

   | The line | Say |
   |---|---|
   | names another store | this folder reads the notes of a store it is not inside - name both. It was moved out of that workspace; `/nk:init` here, or moving it back, settles which it belongs to |
   | names a project this store does not hold | the line is broken - name the line and `/nk:doctor` |

   **Two project lines on the path** - a block in a subfolder as well as at the repository root - is
   resolved by the nearer, and the session is reading both projects' notes, because the harness loads
   every `CLAUDE.local.md` from the working directory up. Say so, naming both files.
2. **The registry.** No line followed -> match the working directory against every `dirs:` entry in
   the store's `## Projects`: a match is the entry's directory or any folder below it. The longest
   matching entry wins.
3. No match -> the working directory is in no registered project. Resolve only through `--project <name>` or the item's `project:` field, never by the directory's name; a skill that needs a project and has none refuses and points at `/nk:init`.

**What `dirs:` is still for: reaching a project from outside its folder.** A folder that resolves by
its read line needs no registry entry to be worked in, but a project you are not standing in - a
dependency's branch, a read line `/nk:doctor` checks, a repository `/nk:project` reports on - is
reachable only through its registered path. Finding it any other way would mean scanning.

### A folder the registry has lost

**Renaming or moving a repository inside its workspace leaves its `dirs:` entry behind.** The read
line moved with the folder, so step 1 still resolves it; the registry still names the old path. When
step 1 resolves the project and the working directory is under none of the store's `dirs:` entries,
probe that project's entry - `Read` `<dir>/.git/HEAD`, then `<dir>/.git`:

| The registered directory | Say |
|---|---|
| **holds no `.git`** | one line: *`<name>`'s registered folder `<dir>` is gone, and this folder carries its read line - `/nk:init` here updates it.* |
| **still holds one** | nothing - a second folder for one project is not handled |

**It is information, not a question**, so it repeats until `/nk:init` runs and asks nothing.
**Nothing is written**: an absent path is as likely to be an unmounted volume as a renamed folder,
and updating the registry is `/nk:init`'s. Under `--oneline` it rides in the detail as
`<name> moved`.

Directories, listings and sizes come from `Glob` and `Read`. This plugin runs no shell and no git; where a skill needs a repository fact, it reads the file under `.git` that holds it. `Write` creates the directories it needs on the way to a file.
