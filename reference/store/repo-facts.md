---
doc:   repo-facts
title: Reading a repository's facts - git answers, and what a run without it skips
---

# Reading a repository's facts

**Git answers every repository fact.** Run it as an ordinary command in whatever shell you have -
the commands below read the same in every one. **The plugin grants nothing**: whether a call runs
quietly, asks, or is refused is the user's permissions and mode, and a call that asks is let ask -
never worked around.

**Where it runs.** In **the repository you are standing in**, run bare `git <command>` in the
working folder - the root, branch, commit, origin and *commits since* forms below, and
`git status --porcelain`, run there without asking; the others may ask. **Any other repository is
`git -C <dir> <command>`** - it asks the user once, and their answer can cover later calls. Never
`cd` into a repository to avoid `-C`, and never point git elsewhere with `--git-dir`.

**Which repository.** For a project, start from the working folder when it resolves to that project
- by its read line or a `dirs:` match, per the store-resolution rule's *Resolving a project inside
the store* - **and from the project's `dirs:` entry when it does not**: a project you are not
standing in is reachable only through its registered path. Where no project is involved -
`/nk:init` finding the root it will register - start from the folder it was given.

| Fact | Command | Reads as |
|---|---|---|
| the root | `git rev-parse --show-toplevel` | the path printed. *Not a git repository* is **an answer** - there is no repository - never *git unavailable* |
| the branch | `git branch --show-current` | the name; **empty is a detached HEAD**, which names no branch |
| the commit | `git rev-parse HEAD` | the full sha, for a verified stamp |
| ignored | `git check-ignore -q <path>` | exit 0 ignored, 1 not - every rule git applies, patterns and negations included. **It asks even in the working folder** |
| the exclude file | `git rev-parse --git-path info/exclude` | a path, relative to where git ran - a worktree's resolves to the shared one |
| the origin | `git config --get remote.origin.url` | the URL; exit 1 is *no origin* |
| commits since | `git log --since=<date> --oneline` | one line each, newest first |
| files changed since | `git log --since=<date> --name-only --format=` plus `git status --porcelain` | the union, each path once |

**A branch or commit you did not read from git or the fallback below is not known** - say there is
none; never take one from a folder name, an item or a guess.

## Git unavailable

**Git is unavailable when it is not installed, the call is refused or denied, or the run is headless
and nobody can approve it.** Then **skip the step and say so** in one line - never guess, and never
reach the fact another way. Each skill names what its skip leaves out. Two facts keep a fallback,
read with the file tools:

- **The branch**: `Read` `<repo>/.git/HEAD`, walking up from the starting folder to the first that
  exists. `ref: refs/heads/<branch>` names it; a bare sha is a detached HEAD. A `.git` that is a
  file, or no `HEAD`, leaves the branch unknown.
- **The commit**: a detached HEAD's sha is its own; otherwise `Read` `<repo>/.git/refs/heads/<branch>`.
  Missing leaves the commit unknown - the stamp is written *unverified*.
