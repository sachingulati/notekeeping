---
description: Create a store - global, workspace, or project. Creates structure; never mines your material.
argument-hint: "[path] [--workspace] [--project] [--caller <name>]"
allowed-tools: Read, Glob, Edit, Write, Bash(git rev-parse:*)
---

Create a store. **It creates structure - the directories, a config, and the two documents a project
needs in order to exist. It never mines your material for content.**

**`--caller <name>`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: **report,
never create.** Say what you would have created and that a person has to run it, in the outcome
line's detail.

**It reads exactly one class of thing**: the repository's own orientation sources - a build file, a
`README.md`, the repo's own `CLAUDE.md` - and only to fill `overview.md`'s fields, which are facts
about what the project *is*. Nothing it reads becomes an entry in a register.

**It does not guess a project map, and it reads no notes.** A notes folder, a wiki export, another
tool's store, the knowledge sitting in a `CLAUDE.md`: none of it is read here. Bringing that material
in is `/nk:adopt`'s job. This command is not a substitute for it and must not improvise one.

## The three forms

| Form | Does |
|---|---|
| `/nk:init [path]` | **the whole setup, working outward from where you are.** Below |
| `/nk:init --workspace [path]` | create the workspace store, and stop there - **plus the offer of the repositories under it**, per `## Workspace` |
| `/nk:init --project [path]` | create the project inside the workspace above it, and stop there |

`[path]` defaults to the working directory in every form.

**Detecting is not guessing: every level confirms before it writes**, and the report names everything
written.

---

## `/nk:init` - the automatic form

**Work outward from the repository you are standing in**, rather than asking for an abstraction
first.

1. **Create global if it is missing.** `~/.notekeeping/` is a fixed home - no judgment, nothing to
   ask. Say that it was created.

2. **Decide the project directory.** `git rev-parse --show-toplevel` on `[path]`:
   a repository → that root is the project. **Not a repository, or git cannot answer → ask** whether
   to treat this directory as a project, and stop if the answer is no. **Never infer a repository
   from the filesystem** - `## Project` step 2 has the rule.

3. **Resolve the workspace before creating the project.** A project **cannot exist without one** -
   it lives at `<workspace>/.notekeeping/projects/<name>/`, so there is nowhere to put it otherwise.
   Walk up per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`:

   | What is above | Do |
   |---|---|
   | a workspace store already | use it. Nothing to ask |
   | no workspace, **and the parent is `$HOME`** | **refuse** - see below |
   | no workspace, parent is anything else | **propose the parent as the workspace and ask** |

4. **Create the workspace if one was agreed**, per `## Workspace` below - **including the offer of
   the other repositories under it.** The sibling clones are found at the same moment and there is
   no reason to make the user come back for them.

5. **Create the project**, per `## Project` below.

**Run it again later and the same path does less.** Inside a repository under a workspace that
already exists, steps 1, 3 and 4 all find their work done and it simply registers the project. That
is the *"once per repository cloned later"* case, and it needs no flag.

### `$HOME` cannot be a workspace

A workspace store at `$HOME` would be `~/.notekeeping/` - **global's fixed home** - collapsing the
two scopes into one file set. So when the parent is `$HOME`, **stop and say why**:

> A workspace at your home directory would be the same folder as the global store. Put your
> repositories under a directory - `~/projects`, say - and run `/nk:init` there, or name one with
> `/nk:init --workspace <path>`.

Do not offer to continue without a workspace, and do not create the project at global scope. Global
holds knowledge that is **not about any codebase**; a project there is a category error, not a
shortcut.

---

## Global - `~/.notekeeping/`

The fixed home for knowledge that is not about any codebase, and an **ancestor of every workspace**,
which is what makes it reachable everywhere without configuration.

```
~/.notekeeping/
  config.md          schema_version: <the shipped version>
```

**Global's `config.md` is a store config *and* the machine config.** It carries one setting -
`schema_version` - plus the `## Workspaces` registry, which is not a setting. A store
written without `schema_version` has no version at all, and **an absent version is never assumed to
be the current one** - every later upgrade check has to stop and ask.

**Create no knowledge files.** An empty `gotchas.md` is a stub, and a stub is worse than nothing: it
looks answered. Files appear when promotion first writes to them.

---

## Workspace - `<workspace>/.notekeeping/`

A workspace is a directory holding connected projects. **The user names it; never infer one.**

```
<workspace>/.notekeeping/
  config.md          schema_version: <the shipped version>, and nothing else
  schema/            the user's overlay lives here
  projects/
  work/
```

**`config.md` is the only thing this command writes here.** The three directories below it are the
shape the store takes, **not a list to create**: nothing in this command's `allowed-tools` makes an
empty directory - `Write` creates parents only on the way to a file - and the portability rule in
`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md` rules out reaching for a shell to do it.
**Each appears when something is first written into it**, which is what *Create no knowledge files*
above already says for files. **Do not report them as created, and do not treat their absence as a
failed init.**

The store's own knowledge files - `NOTES.md`, `domain.md`, `gotchas.md`, `decisions.md` - sit
directly in `.notekeeping/` when promotion creates them. **A store's own scope is not a subdirectory
of itself**, so do not create a `workspace/` or `global/` folder inside it.

**Confirm the path once, before anything is written**, and say which directory you will use. Never
ask again on later runs.

**Creating the workspace writes the workspace projection** - `<workspace>/CLAUDE.local.md`, per
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`. It has nothing to carry yet; write it anyway, since
the file and its markers are what later saves update in place.

### Then offer the projects that are already there

**Whenever a workspace is created - by either route - offer the repositories under it.** The
workspace is the one decision; the projects under it are a consequence, and the sibling clones are
already in front of you.

1. **`git rev-parse --show-toplevel` on the named path and on each of its *direct* children.** Depth
   one, no recursion. A child is offered only when git returns a root for it.
2. **If git is unavailable, offer nothing and say so.** Do not glob for `.git/` - `## Project`
   step 2 forbids it. Point at `/nk:init` inside each repository instead.
3. **List what you found and let them choose.** Nothing is registered without an explicit answer,
   and the default is worth stating: propose all of them, since the user named the parent.
4. **For each chosen repository, run `## Project` steps 3-6** - propose and confirm the name, write
   `NOTES.md` and `overview.md`, register the root, and write the repo projection and its ignore
   entry. **Report only what you actually wrote.**
5. **None found, or none chosen, is a normal outcome.** Say the workspace is ready and name the next
   step. An empty workspace is not a failure.
6. **Reached from the automatic form, the directory you started in is already being registered** by
   its step 5 - do not offer it twice, and do not register it twice.

**This stays inside the no-scanning rule.** It enumerates directory entries one level below a path
the user just named, asks about each, and reads nothing inside any of them. Reading a user's
material and inferring a project map from it is `/nk:adopt`'s job.

---

## Project - `<workspace>/.notekeeping/projects/<name>/`

**Registering is what stops a project from being guessed**, and from resolution falling back to a
directory's basename.

1. **Resolve the workspace store** per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. No store
   above this directory means **refuse** and point at a workspace init - do not create one silently.
2. **Find the repo root** with `git rev-parse --show-toplevel`, and handle three outcomes, not two:

   | What is true | What you record |
   |---|---|
   | a repository, git present | the repo root - resolution is automatic from any subdirectory after this |
   | **not** a repository, git present | no root; the project resolves by name |
   | **git unavailable, or you cannot run it** | **unknown.** Say so, and record no root |

   **The third case is neither of the others.** Recording *"no repository"* when you could not tell
   makes every later git-dependent behaviour report *not applicable* for a project that may well be
   a repo, and nothing corrects it. Substituting the filesystem is the mirror error: a `.git/` found
   by glob is an inference, and once written it is indistinguishable from a root git returned.
   **Record a repo root only from `git rev-parse --show-toplevel`** -
   `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`.
3. **Propose the name and confirm it.** The repository's directory name is a *proposal*, not an
   answer - say which name you will use and let them correct it. **A project need not be a repo:** an
   initiative with no clone is a first-class case, and resolves by name.
4. **Create the directory, `NOTES.md` and `overview.md`.** Those are the two documents `/nk:project`
   owns. **Resolve both definitions per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`
   before writing either** - the user's overlay wins over the shipped
   `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/project/{NOTES.md,overview.md}`. Write the
   structure each definition specifies and nothing beyond it.

   **These two are not stubs**: they say where things are and what this project is, and a project
   without them has nowhere to point. **Create no registers and no ledger** - not `gotchas.md`, not
   `decisions.md`, not `domain.md`. Those appear when promotion first writes to them.

   **Report only what you actually wrote.** If a definition could not be resolved, say so and create
   nothing rather than describing a file that is not there.
5. **Register the repo root** so later sessions resolve without asking again.
6. **Write the repo projection**, per `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - the file,
   and the ignore entry it orders. **Registration is what creates a projection, not the first save**,
   so a project that has just been registered is already delivering. **Never report a file you did
   not write** - the ordinary reason one is absent is that its source has no content, which is an
   empty render rather than a skip.

   **A repo that already holds a `CLAUDE.local.md` of the user's own is the ordinary case here.**
   Append below it, never modify a byte above it, and search the whole file for the markers first.

   **Only this project's file.** The repo projection carries one project, so several registered in
   one run get one write each and nothing is concatenated.

---

## Refuse a non-empty directory

**This is a check on a store being created, and only there.** A store that already exists is the
ordinary re-run above - step 1, 3 or 4 finding its work done - and it is never refused for holding
the notes it is supposed to hold.

If the target store directory is one this run would create and it exists with any content,
**stop**. Say:

> `<path>` is not empty - it has <what you saw>. `/nk:init` only creates an empty store, so it
> will not touch this one.

**Then stop and name `/nk:adopt`** - reading what is already there and building the store from it
is that command's job. The other two ways out are pointing `/nk:init` at a different path, or moving
the existing content aside.

Do not create a store around material nobody has looked at. This is the one check `init` makes.

## The config files

**Write exactly one setting: `schema_version`** - in a workspace store config and in global's alike.
**Take its value from `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`**, where the shipped
version is stated; a number typed into this file drifts the first time the schema moves.
Every other setting has a documented default and is edited by hand; a wall of commented settings is
unreadable. **Never invent a setting name**: a plausible key the plugin does not read looks
configured.

**The `## Projects` registry is not a setting.** Project init writes a project's `dirs:` there -
step 5 above - and a store config may hold that mapping.

**Nor is global's `## Workspaces` registry.** **Creating a workspace store appends its absolute path
there**, in `~/.notekeeping/config.md` - **creating global first where it is missing**, since
`--workspace` on a fresh machine reaches this step with no file to append to - so that a machine's
workspaces can be enumerated at all -
`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md` has what it is and is not for. **Append; never
rewrite the list**, and do not add an entry for a workspace that already has one. This is the only
thing workspace creation writes into global.

## Report

State **every level you created**, the exact path of each, and every file written - including each
project registered by the offer. The automatic form routinely creates three at once, so report them
as a list rather than a sentence.

Then the single next step: `/nk:work` to start something, `/nk:init` inside a repository cloned
later to add it, or `/nk:help` to see what exists.

**Say how many calls are left.** After the automatic form there are none for what already exists -
global, the workspace and its repositories are done - so the user does not think this runs once per
level forever.

**Name every write outside the store, separately.** A projection is written at registration, so each
registered repo gets a `CLAUDE.local.md` and a `.git/info/exclude` line, and the workspace root gets
one too. **List them by absolute path**, and say which were *appended to* rather than created. This
report is the only moment the user sees the files they just agreed to in their own trees.

Nothing else on disk was touched. Say so literally: anything written that is not on that list is
named instead.
