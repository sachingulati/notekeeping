# Project - `<workspace>/.notekeeping/projects/<name>/`

**Registering is what stops a project from being guessed**, and from resolution falling back to a
directory's basename.

1. **Resolve the workspace store.** No store above this directory means **refuse** and point at a
   workspace init - do not create one silently.
2. **Find the repo root with git** - the repo-facts rule's *the root*, run from `[path]`. Three
   outcomes: **a root**; **not a git repository** - say so, and record no root; **git unavailable** -
   offer `[path]` itself as the root and let the user confirm it or name another, saying git could not
   be asked. **Record a root only from git or the user's answer**, never from a folder name. **A
   project with no root resolves only through `--project <name>` or the item's `project:` field** -
   never by directory name, which resolution never falls back to.
3. **A project already here?** Before proposing a name, resolve the root per the
   store-resolution rule's *Resolving a project inside the store*. A root that resolves is not a new project:

   | It resolves | Do |
   |---|---|
   | by a `dirs:` entry | nothing to register - say which project, and stop |
   | by its read line, **the project's registered folder gone** (that rule's *A folder the registry has lost*) | **this is that project, moved.** Propose replacing the entry that is gone with this root, naming the old path and the new; on a yes, rewrite that one entry and nothing else - its `NOTES.md`, `overview.md` and read line already exist. Never create a second project for it |
   | by its read line, the registered folders still there | **another folder of that project** - a mirror, a second clone. Ask: *Add it to `<name>` / Register it as its own project / Stop*. *Add* is step 5 alone - its read line already exists |

   **Propose the name and confirm it.** The repository's directory name is a *proposal*, not an
   answer - say which name you will use and let them correct it. The name is what `--project <name>`
   will name, not a resolution path in itself. **A name the store already holds is not a new
   project**: ask *Add this folder to `<name>` / Choose another name*. *Add* skips step 4 - the
   project's files exist - and does steps 5 and 6, so the folder reads that project's notes.

   **Joining never merges two projects.** A folder that resolves to a project of its own is that
   project's, and init does not move it to another: say so, and that the two projects' notes are
   combined by hand.
4. **Create the directory, `NOTES.md` and `overview.md`.** Those are the two documents `/nk:project`
   owns. **Resolve both definitions before writing either** - the user's overlay wins over the
   shipped ones. Write the structure each definition specifies and nothing beyond it.

   **These two are not stubs**: they say where things are and what this project is, and a project
   without them has nowhere to point. **Create no registers and no ledger** - not `gotchas.md`, not
   `decisions.md`, not `domain.md`. Those appear when promotion first writes to them.

   **Report only what you actually wrote.** If a definition could not be resolved, say so and create
   nothing rather than describing a file that is not there.
5. **Register the repo root** - its `dirs:` in the store config's `## Projects` - so later sessions
   resolve without asking again. **Append the line; never rewrite another.** A folder joining a
   project goes after that project's last line, so its main folder stays first.
6. **Write the repo read line**, per the projections and projection-writing rules - the file, and the ignore entry it orders.
   **Registration is what writes the line, not the first save**, so a project that has just been
   registered is already delivering. **Never report a file you did not write.**

   **A repo that already holds a `CLAUDE.local.md` of the user's own is the ordinary case here.**
   Append below it, never modify a byte above it, and search the whole file for the markers first.

   **A repo with an `AGENTS.md` and no `CLAUDE.md` or `.claude/CLAUDE.md` importing it** stops
   loading that `AGENTS.md` once the read line exists - Claude Code reads `AGENTS.md` only where no
   `CLAUDE.md` or `CLAUDE.local.md` is present. `Read` `<repo>/AGENTS.md` before the write; where it
   exists, say so in the report, with the line that brings it back - `@AGENTS.md`, above the block in
   that `CLAUDE.local.md` - which is the user's to add. Write nothing for it.

   **Only this project's line.** The repo line reads one project's notes, so several registered in
   one run get one write each and nothing is concatenated. **The one exception is a repo at the
   workspace root** - a monorepo: its file already carries the workspace's line, so the block is
   rendered with both, the workspace's first.
