# Workspace - `<workspace>/.notekeeping/`

A workspace is a directory holding connected projects. **The user names it; never infer one.**

```
<workspace>/.notekeeping/
  config.md          schema_version: <the shipped version>; ## Projects once one is registered
  NOTES.md           the title and header only, until promotion first writes to it
  schema/            the user's overlay lives here
  projects/
  work/
```

**`config.md` and `NOTES.md` are the only files this form writes here.** `NOTES.md` is the target
of the workspace read line, so it exists from the start: its `# <workspace>` title and its header
comments, per the `NOTES.md` definition, and **no empty heading**. The three directories are the
shape the store takes, **not a list to create**: nothing in this skill's `allowed-tools` makes an
empty directory - `Write` creates parents only on the way to a file - and no shell command is
run to do it. **Each appears when something is first written into it**, as the store's knowledge files
do. **Do not report them as
created, and do not treat their absence as a failed init.**

The store's other knowledge files - `domain.md`, `gotchas.md`, `decisions.md` and the rest - sit
directly in `.notekeeping/` when promotion creates them. **A store's own scope is not a subdirectory
of itself**, so do not create a `workspace/` or `global/` folder inside it.

**Confirm the path once, before anything is written**, and say which directory you will use. Never
ask again on later runs. **Find the repositories below first - it is only reading - and ask the path
and the pick in the same question.**

**Creating the workspace writes the workspace read line** - `<workspace>/CLAUDE.local.md`, per the
projections rule: one line that reads the workspace's `NOTES.md`. The store is written first and the
line last.

## Then offer the projects that are already there

**Whenever a workspace is created - by either route - offer the repositories under it.** The
workspace is the one decision; the projects under it are a consequence, and the sibling clones are
already in front of you. **Ask git about the named path; list its children.**

1. **The named path itself.** The repo-facts rule's *the root*, from `<workspace>`: a root equal to
   `<workspace>` → the named path is a repository - a monorepo root - and it is offered with the rest.
   A root above it is a repository the workspace sits inside, not one to offer; git unavailable skips
   this step, said.
   **Registered, its one `CLAUDE.local.md` delivers both scopes**: one block, the workspace's line
   first, then the project's, per the projections rule.
2. **Its *direct* children - one `Glob`, this exact pattern:** `<workspace>/*/{.git,.git/HEAD}`. A
   hit on `<child>/.git/HEAD` is a repository; a hit on `<child>/.git` itself is a file - a worktree
   or a submodule - and is one too. **The braces are the point**: `*/.git/HEAD` and `**/.git/HEAD`
   both miss a worktree, and a `**` over the whole workspace walks every dependency folder in it. Depth one, no recursion: a repository nested deeper is not offered. **Never `Glob` for `.git`
   alone** - a glob returns files, so it misses every ordinary repository, whose `.git` is a folder.
   **A zero is not yet an absence**: confirm it with `<workspace>/**/.git/HEAD`, keeping only the hits
   one level below `<workspace>`, before you believe it - the walk rule's *A pattern that matches
   nothing*.
3. **List what you found and let them choose**, beside the path confirm. Nothing is registered
   without an explicit answer, and the default is worth stating: propose all of them, since the user
   named the parent.
4. **For each chosen repository, run the project form's steps 3-6** - propose and confirm the name,
   write `NOTES.md` and `overview.md`, register the root, and write the repo read line and its ignore
   entry. **Report only what you actually wrote.**
5. **None found, or none chosen, is a normal outcome.** Say the workspace is ready and name the next
   step. An empty workspace is not a failure.
6. **Reached from the automatic form, the directory you started in is already being registered** by
   its step 5 - do not offer it twice, and do not register it twice.

**This stays inside the no-scanning rule.** It lists directory entries one level below a path the
user just named, asks about each, and reads nothing inside any of them but the `.git` the listing matched. Reading a user's material and inferring a project map from it is `/nk:adopt`'s job.
