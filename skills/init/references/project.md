# Project - `<workspace>/.notekeeping/projects/<name>/`

**Registering is what stops a project from being guessed**, and from resolution falling back to a
directory's basename.

1. **Resolve the workspace store.** No store above this directory means **refuse** and point at a
   workspace init - do not create one silently.
2. **Find the repo root by reading, never by running git** - the walk rule's *Which repository*:
   from `[path]` up to the filesystem root, the nearest folder where `Read` `<folder>/.git/HEAD`
   succeeds, or where `<folder>/.git` is itself a file with a `gitdir:` line - a worktree or a
   submodule. **Never `Glob` for `.git` alone**: a glob returns files, so it misses every ordinary
   repository, whose `.git` is a folder. Two outcomes: **a root**, or **no `.git` found** - say so,
   and record no root. **Record a root only from a `.git` you read**, never from a folder name. **A
   project with no root resolves only through `--project <name>` or the item's `project:` field** -
   never by directory name, which resolution never falls back to.
3. **A project already here?** Before proposing a name, resolve the root per the
   store-resolution rule's *Resolving a project inside the store*. A root that resolves is not a new project:

   | It resolves | Do |
   |---|---|
   | by a `dirs:` entry | nothing to register - say which project, and stop |
   | by its read line, **the project's registered folder gone** (that rule's *A folder the registry has lost*) | **this is that project, moved.** Propose replacing its `dirs:` entry with this root, naming the old path and the new; on a yes, rewrite that one entry and nothing else - its `NOTES.md`, `overview.md` and read line already exist. Never create a second project for it |
   | by its read line, the registered folder still there | a second folder for one project is not handled - say so, and that registering this one as its own project replaces the line with the new project's. Go on only on a yes |

   **Propose the name and confirm it.** The repository's directory name is a *proposal*, not an
   answer - say which name you will use and let them correct it. The name is what `--project <name>`
   will name, not a resolution path in itself.
4. **Create the directory, `NOTES.md` and `overview.md`.** Those are the two documents `/nk:project`
   owns. **Resolve both definitions before writing either** - the user's overlay wins over the
   shipped ones. Write the structure each definition specifies and nothing beyond it.

   **These two are not stubs**: they say where things are and what this project is, and a project
   without them has nowhere to point. **Create no registers and no ledger** - not `gotchas.md`, not
   `decisions.md`, not `domain.md`. Those appear when promotion first writes to them.

   **Report only what you actually wrote.** If a definition could not be resolved, say so and create
   nothing rather than describing a file that is not there.
5. **Register the repo root** - its `dirs:` in the store config's `## Projects` - so later sessions
   resolve without asking again.
6. **Write the repo read line**, per the projections rule - the file, and the ignore entry it orders.
   **Registration is what writes the line, not the first save**, so a project that has just been
   registered is already delivering. **Never report a file you did not write.**

   **A repo that already holds a `CLAUDE.local.md` of the user's own is the ordinary case here.**
   Append below it, never modify a byte above it, and search the whole file for the markers first.

   **Only this project's line.** The repo line reads one project's notes, so several registered in
   one run get one write each and nothing is concatenated. **The one exception is a repo at the
   workspace root** - a monorepo: its file already carries the workspace's line, so the block is
   rendered with both, the workspace's first.
