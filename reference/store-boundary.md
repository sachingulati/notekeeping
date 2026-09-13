---
doc:   store-boundary
title: Resolving a store, and staying inside it
---

# The store boundary

**Resolve the store first, before reading or writing anything. Every read and every write stays
inside it.**

A **store** is a `.notekeeping/` directory. There are two kinds and no others:

| Store | Where | Its scope |
|---|---|---|
| **global** | `~/.notekeeping/` — a fixed path, always | your working life, not any codebase |
| **workspace** | `<workspace>/.notekeeping/` | the projects that sit in that workspace |

**A store's own scope sits at its root; every narrower scope is a subdirectory.** So a workspace
store holds `projects/<name>/` and `work/<bucket>/<id>/` beneath it, and its own files —
`NOTES.md`, `domain.md`, `gotchas.md`, `decisions.md` — sit directly in `.notekeeping/`. There is no
`global/` directory: `~/.notekeeping/` **is** global scope.

## Resolving it

1. **Walk up** from the working directory to the nearest `.notekeeping/`. That is the workspace
   store. **Test the exact path at each level** - does `<dir>/.notekeeping` exist - rather than
   listing a directory and reading what comes back. **A store is a hidden directory and the
   ordinary listing does not show one:** a glob of `*` does not match it, and a plain `ls` omits
   it, so a walk built that way reports *no store* while standing next to one.
   *(Measured, session 26: one attempt in nine walked `ws`, the fixture root, `Temp`, `Local`,
   `AppData` and `~`, **named all six correctly**, and missed the `.notekeeping/` sitting in the
   first of them. It satisfied the name-the-walk rule below and was still wrong - which is why
   this step is now about **how you look**, and not only about saying where you looked.)*
2. `~/.notekeeping/` is **global**, and is an ancestor of every workspace, so it is always in scope.
3. **Found nothing?** Stop and say so. Point at `/nk:init`. Do not continue with a guess.
   **Name the directories you actually walked.** *"No store above here"* is a claim about every
   ancestor, and it is worth exactly as much as the walk behind it - so make the walk visible and
   the claim checkable. **Do not report an absence you did not look for.**
   *(Measured: a `/nk:save` once refused with "no workspace-level `.notekeeping` exists between the
   repo and there" while one sat in the immediate parent, registering the very project it said was
   unregistered. It had looked at `~` and not at the walk.)*
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

A person may keep several piles of notes; none of them is a store unless it has been initialised as
one, and the rest are none of this plugin's business.

**Measured, and this is why the rule is written down.** A bare `/nk:load` run from a home directory
once resolved against a completely different notes store - reading real work items, real branch
state and a real colleague's name - while the configured root sat empty and unread. It was harmless
only because `load` writes nothing. `save` resolves a target the same way, and `save` writes.
*(Under this rule that run refuses: there is no `.notekeeping/` above a bare home directory.)*

## Staying inside it

| | |
|---|---|
| Reads | the resolved workspace store, plus `~/.notekeeping/`. Plus the repository you are working in, **for git state** - and for a repo's own `CLAUDE.md` or `README.md` as a *source* when assembling `overview.md`, never as a target |
| Writes | **inside a store, plus the three named targets below - and that list is closed. Every one of them is personal and local; none is a file anyone else reads.** The machine config is `~/.notekeeping/config.md`, which is a store |
| Grep and glob | rooted at a resolved store - never at `~`, never at the working directory |
| A path in a query | resolved relative to the store, and refused if it escapes it |

**Everything outside `.notekeeping/` is the user's.** That is the whole boundary, and it is why the
directory is named the way it is: the plugin claims no ordinary word at any level. Repositories,
loose files, personal scratch and anything else are read only when the user names them, and never
reorganised or rewritten.

### The three writes that land outside a store

**This row used to read *there is no exception*, and that stopped being true when the projections
began shipping enabled.** The property it protects was never *never outside a store* - it is **only
into a target somebody had to name**. So the three are listed here rather than left to each command
to carve out for itself: a rule that is quietly false is a rule the next reader is entitled to
ignore, and this one is cited by most of them.

**There were four until graduation was removed, and the fourth was the different one.** It wrote
`<repo>/CLAUDE.md` - committed, team-visible, code-reviewed. **The three that remain are two
`CLAUDE.local.md` files and a `.git/info/exclude` entry: all personal, none committed, none read by
anybody but you.** That is a stronger property than the list had before, and it is worth stating as
a property rather than leaving it to be inferred from three rows.

| What | Target, and what bounds it | Written by |
|---|---|---|
| the repo projection | `<repo>/CLAUDE.local.md` - only a repository **registered as a project** | `save`, `project`, `doctor --fix` |
| the workspace projection | `<workspace-root>/CLAUDE.local.md` - only the directory holding that store's `.notekeeping/` | the same three |
| the ignore step | `<repo>/.git/info/exclude` - never `.gitignore`, and written **before** the projection | the same three |

**And one clear, which is not a write to disk:** `/nk:save` drains the harness memory store,
removing **only what this plugin put there** and nothing else.

**Nothing else, ever. A write outside a store that is not on this list is a defect, not a judgment
call.** What each of the three may contain is governed by
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`; this table settles only *which targets exist*, so
that *inside a store* stays checkable rather than approximately true.

## Resolving a project inside the store

**Never invent a project.** A project exists because `/nk:init` created it.

1. Resolve the working directory to a repo root with `git rev-parse --show-toplevel`, **where git
   can answer**.
2. Match that root against the projects registered in the store.
3. **No match → refuse and point at `/nk:init`.** Never fall back to the directory's basename.

**Git has three outcomes, not two**, and the third must not be reported as the second:

| What is true | What you record |
|---|---|
| a repository, git present | the repo root |
| **not** a repository, git present | no root; resolve by name, and git-dependent behaviour is *not applicable* |
| **git unavailable, or you cannot run it** | **unknown.** Say so, and record no root |

**Directories, listings and sizes come from `Glob` and `Read`, never from a shell.** `ls`, `wc` and
`mkdir` were granted to five commands and invoked by none of them; they are gone (6.32). **`git` is
the only shell this plugin ever calls**, and the row above is what makes even that optional - so the
plugin behaves the same on Windows, macOS and Linux, with or without a POSIX shell. `Write` creates
the directories it needs on the way to a file.

*"There is no repository here"* and *"I could not determine whether there is one"* are different
claims. Recording the first when the third is true is unfalsifiable afterwards: every later
git-dependent behaviour reports *not applicable* for a project that may well be a repository.

**And *"the root is here"* is the same error mirrored.** When git cannot answer, do not fall back to
the filesystem - a `.git/` found by glob is an inference, and once written it is indistinguishable
from a root `git rev-parse` returned. **A repo root is recorded only from
`git rev-parse --show-toplevel`; otherwise it is unknown.** This is the rule above applied to the
project instead of the store: *never infer from the filesystem what a declaration is supposed to
tell you.*
