---
doc:   store-walk
title: Staying inside the store
---

## Staying inside it

| | |
|---|---|
| Reads | the resolved workspace store and global; a registered repository's own `CLAUDE.md` or `README.md` as a source; what `/nk:adopt` was pointed at and the context files it reads; and, where a skill needs a repository fact, the file under `.git` that holds it - *Reading a repository's facts*, below. Read these files directly; never run git. |
| Writes | **inside a store, plus the named targets outside a store - and that list is closed. Every one of them is personal and local; none is a file anyone else reads.** The machine config is `~/.notekeeping/config.md`, which is a store |
| Grep and glob | rooted at a resolved store - never at `~`, never at the working directory. **Three exceptions**: `/nk:adopt` inventories the roots it was given and the references they name; `/nk:save` globs `*/memory/*.md` under `~/.claude/projects` to find the store it is draining, which is the only way to address a directory keyed by a slug; and `/nk:init`'s workspace offer globs `<ws>/*/{.git,.git/HEAD}` - the direct children of a path the user just named - to find the repositories under it |
| A path in a query | resolved relative to the store, and refused if it escapes it |

## Reading a repository's facts

**Which repository.** `<repo>` is the nearest folder - from a starting folder up to the filesystem
root - where step 1 or step 2 below finds a `.git`. **For a project, start from the working
directory when it resolves to that project** - by its read line or a `dirs:` match, per the
store-resolution rule's *Resolving a project inside the store* - **and from the project's `dirs:` entry when it does
not**: a project you are not standing in is reachable only through its registered path. Where no
project is involved - `/nk:init` finding the root it will register - start from the working
directory. A subdirectory of a repo is not its root: never read `<cwd>/.git/...` without walking up.

**The branch, in order.**
1. `Read` `<repo>/.git/HEAD`.
2. **If it does not exist, `.git` may be a file** - a worktree or a submodule. `Read` `<repo>/.git`
   itself: its `gitdir: <path>` line names the git directory, and the branch is in `<path>/HEAD`.
   **A relative `<path>` resolves against `<repo>`**, the folder holding the `.git` file -
   a submodule's is usually `../.git/modules/<name>`.
3. `ref: refs/heads/<branch>` names the branch. A bare sha is a detached HEAD, and names none.

**A branch you did not read from one of these files is not known** - say there is none; never take
one from a folder, an item or a guess.

**The commit a branch points at** (a verified stamp): read `<gitdir>/refs/heads/<branch>`; where the
git directory holds a `commondir` file - a worktree - read the ref under `<gitdir>/<commondir>/`
instead. Not there, search `packed-refs` in the same directory for the line ending in
`refs/heads/<branch>`. A detached HEAD's sha is its own commit. **Unreadable means unknown** - leave
that half of the stamp empty rather than guess.

### A pattern that matches nothing

**A `*` segment followed by a literal filename can return zero with the file present.**
`<store>/projects/*/overview.md` can return nothing against a store holding that file, while
`<store>/**/overview.md` finds it. A pattern that **ends** in a wildcard - `<dir>/*/memory/*.md` -
does not fail the same way. Nothing tells you which one you ran: the empty
result is indistinguishable from a real absence.

- **To find a named file at any depth, root the pattern at the store and lead with `**`** -
  `<store>/**/overview.md` - then keep the hits whose path has the shape you wanted. Never put a
  single `*` segment in front of a literal name - except the workspace offer's `<ws>/*/{.git,.git/HEAD}`,
  which needs exactly one level. Its result differs between harness versions, so
  **a zero from it is confirmed with `<ws>/**/.git/HEAD`**, keeping only the hits one level below
  `<ws>`, before it is believed.
- **To address one exact file, `Read` it.** Globbing is for files whose names you do not know.
- **A zero is a finding until a second form agrees.** Before reporting that a definition has no
  files, or that a thing is absent, re-run it as `<store>/**/<name>`. Two forms returning zero is
  an absence; one is not.

**Everything outside `.notekeeping/` is the user's.** Repositories, loose files, personal scratch
and anything else are read only when the user names them, and never reorganised or rewritten.
