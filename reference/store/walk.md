---
doc:   store-walk
title: Staying inside the store
---

## Staying inside it

| | |
|---|---|
| Reads | the resolved workspace store and global; a registered repository's own `CLAUDE.md` or `README.md` as a source; what `/nk:adopt` was pointed at and the context files it reads; and, where a skill needs a repository fact, git - the repo-facts rule. |
| Writes | **inside a store, plus the named targets outside a store - and that list is closed. Every one of them is personal and local; none is a file anyone else reads.** The machine config is `~/.notekeeping/config.md`, which is a store |
| Grep and glob | rooted at a resolved store - never at `~`, never at the working directory. **Three exceptions**: `/nk:adopt` inventories the roots it was given and the references they name; `/nk:save` globs `*/memory/*.md` under `~/.claude/projects` to find the store it is draining, which is the only way to address a directory keyed by a slug; and `/nk:init`'s workspace offer globs `<ws>/*/{.git,.git/HEAD}` - the direct children of a path the user just named - to find the repositories under it |
| A path in a query | resolved relative to the store, and refused if it escapes it |

## A pattern that matches nothing

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
