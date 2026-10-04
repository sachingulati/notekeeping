---
doc:   store-resolve
title: Resolving a store, and the project inside it
---

# The store boundary

**Resolve the store first, before reading or writing anything. Every read and every write stays
inside it.** A heading in *italics* below - *Probing by name* - is a case, kept in the cases file the
skill names; read it when it occurs.

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
   listing a directory and reading what comes back: **`Glob` the exact path `<dir>/.notekeeping/*`,
   or `Read` a file you expect inside it.** Existence is proved by a hit, never by a name's absence
   from a listing - which patterns fail, and how, is *Probing by name*.

   **The walk stops below `~`.** Under your home directory, the last level probed is the one
   directly beneath `~` - `~` itself is global's home, a workspace cannot sit there, and nothing
   above it is yours. Outside your home directory, walk to the filesystem root. **A probe that
   errors or times out is named as unprobed**, not counted as an absence and not reported as an
   outer store: say which levels could not be checked, and carry on with the nearest store found.
2. `~/.notekeeping/` is **global**, and is an ancestor of every workspace, so it is always in scope.
   **Prove it by `Read` of `~/.notekeeping/config.md`**, never by a `Glob` pattern holding `~` -
   see *A ~ path*, below.
3. **Found nothing?** Stop and say so. Point at `/nk:init`. Do not continue with a guess.
   **Name the directories you actually walked.** *"No store above here"* is a claim about every
   ancestor, and it is worth exactly as much as the walk behind it - so make the walk visible and
   the claim checkable. **Do not report an absence you did not look for.**

4. **Found more than one below `~/`?** Workspaces do not nest. **Use the nearest**, and say that the
   outer one exists and is being ignored, naming both paths. Do not refuse - running against the
   obviously-intended store and saying so beats not running at all. `/nk:doctor` reports it as an
   error.

**Other stores on this machine** are listed in global's `config.md`, for enumeration only - never for
resolution: *Knowing that other stores exist*.

## Never infer a store from the filesystem

**A directory that looks like a notes store is not a store.** Do not scan the home directory, and do
not adopt a folder because it contains markdown, a `Tickets/` directory, a `notes/` directory, or
files shaped like notes. **Only an exact `.notekeeping/` counts.**

**Resolving against the wrong pile is not a harmless error.** `load` only reads, but `save`
resolves a target the same way and `save` writes.

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
   `<name>` is the project. Otherwise do not follow it: *A read line that is not followed*. A
   project line in a subfolder as well as at the repository root: *Two project lines on the path*.
2. **The registry.** No line followed -> match the working directory against every `dirs:` entry in
   the store's `## Projects`: a match is the entry's directory or any folder below it. The longest
   matching entry wins.
3. No match -> the working directory is in no registered project. Resolve only through `--project <name>` or the item's `project:` field, never by the directory's name; a skill that needs a project and has none refuses and points at `/nk:init`.

**A project you are not standing in** is reached only through its `dirs:` entry: *Reaching a project
from outside its folder*. **Step 1 resolved the project, and the working directory is under none of
the store's `dirs:` entries?** The folder was renamed or moved: *A folder the registry has lost*.

Directories, listings and sizes come from `Glob` and `Read`. A repository fact comes from git, per the repo-facts rule; nothing else needs a shell. `Write` creates the directories it needs on the way to a file.
