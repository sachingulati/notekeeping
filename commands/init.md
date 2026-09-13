---
description: Create a store - global, workspace, or project. Makes directories only; never scans or infers.
argument-hint: "[path] [--workspace] [--project] [--caller <name>]"
allowed-tools: Read, Glob, Bash(git rev-parse:*), Write
---

Create an empty store. **This command is deliberately dumb: it makes directories.**

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` - and note what it says about this command:
**you report, and you never create.** Say what you would have created, and that a person has to run
it. A store is a person's act, and that is unchanged by who is asking.

**Emit the outcome line and nothing else.** Explaining the refusal *above* the line is the
narration the contract forbids - the explanation belongs in `<detail>`, on the line itself.

It does not look inside your repositories, does not guess a project map, and does not read a single
file of the user's. If they already have notes, a `CLAUDE.md`, or another tool's store, that is the
job of `/nk:adopt` - **which does not ship yet.** This command is not a substitute for it and must
not improvise one.

## The three forms

| Form | Does |
|---|---|
| `/nk:init [path]` | **the whole setup, working outward from where you are.** Below |
| `/nk:init --workspace [path]` | mark this directory a workspace, and nothing else |
| `/nk:init --project [path]` | mark this directory a project, and nothing else |

`[path]` defaults to the working directory in every form.

**Detecting is not guessing: every level confirms before it writes**, and the report names everything
written.

---

## `/nk:init` - the automatic form

**You are almost always standing in a repository when you decide to keep notes**, so this works
outward from there rather than asking you to name an abstraction first.

1. **Create global if it is missing.** `~/.notekeeping/` is a fixed home - no judgment, nothing to
   ask. Say that it was created.

2. **Decide the project directory.** `git rev-parse --show-toplevel` on `[path]`:
   a repository → that root is the project. **Not a repository, or git cannot answer → ask** whether
   to treat this directory as a project, and stop if the answer is no. **Never infer a repository
   from the filesystem** - `## Project` step 2 forbids it and the measurement is there.

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

### `$HOME` cannot be a workspace, and this is a refusal rather than a caution

A workspace store is `<workspace>/.notekeeping/`. With `<workspace>` as `$HOME` that is
`~/.notekeeping/` - **which is global's fixed home.** The two stores would be one directory: global
and workspace scope collapsed into a single file set, with the rule that there is *exactly one
workspace store between you and global* satisfied by having none.

So when the parent is `$HOME`, **stop and say why**:

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
  config.md          schema_version: 1
```

**Global's `config.md` is a store config *and* the machine config**, and it carries one key. A store
written without `schema_version` has no version at all, which `07-adoption.md` section 5 says must
never be assumed - every later upgrade check has to stop and ask.

**Create no knowledge files.** An empty `gotchas.md` is a stub, and a stub is worse than nothing: it
looks answered. Files appear when promotion first writes to them.

**Write no projection flag.** `projections.enabled` and `projections.workspace` both default
`true`, so a fresh store leaves both unwritten and both on. There is no `workspace_root` to set: the
workspace projection's target is the directory holding that store's `.notekeeping/`, which
resolution already produced.

**`init` still writes nothing outside the store, and that is worth checking rather than assuming.**
Projections are written by `/nk:save`, not by `init`; a fresh store has no registered project, so
the repo projection has no target; and it creates no knowledge files, so the workspace projection
has no source. **No write-on-install survives the default flip** - what changed is that the user
does not have to find a flag before the *first save* delivers anything.

**Say that projections are on.** One line in the report, naming both targets in the shape they will
take. A user who does not want them runs `/nk:config set projections.enabled false`, and they should
learn that here rather than from a file appearing in a repository.

---

## Workspace - `<workspace>/.notekeeping/`

A workspace is a directory holding connected projects. **The user names it; never infer one.**

```
<workspace>/.notekeeping/
  config.md          schema_version: 1, and nothing else
  schema/            empty - the user's overlay lives here
  projects/
  work/
```

The store's own knowledge files - `NOTES.md`, `domain.md`, `gotchas.md`, `decisions.md` - sit
directly in `.notekeeping/` when promotion creates them. **A store's own scope is not a subdirectory
of itself**, so do not create a `workspace/` or `global/` folder inside it.

**Confirm the path once, before anything is written**, and say which directory you will use. Never
ask again on later runs.

**Creating the workspace writes the workspace projection** - `<workspace>/CLAUDE.local.md`, per
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, and skipped when `projections.workspace` is off.
It has nothing to carry yet, and that is not a reason to defer it: the file and its markers are what
later saves update in place, and a store whose delivery target appears only after the first promotion
gives `/nk:doctor` a finding on a store nobody has done anything wrong to.

### Then offer the projects that are already there

**Whenever a workspace is created - by either route - offer the repositories under it.** The
workspace is the one decision; the projects under it are a consequence, and the sibling clones are
already in front of you.

1. **`git rev-parse --show-toplevel` on the named path and on each of its *direct* children.** Depth
   one, no recursion. A child is offered only when git returns a root for it.
2. **If git is unavailable, offer nothing and say so.** Do not glob for `.git/`: recording a repo
   root the filesystem implied rather than git returned is the defect `## Project` step 2 already
   forbids, and it is measured. Point at `/nk:init` inside each repository instead.
3. **List what you found and let them choose.** Nothing is registered without an explicit answer,
   and the default is worth stating: propose all of them, since the user named the parent.
4. **For each chosen repository, run `## Project` steps 3-5** - propose and confirm the name, write
   `NOTES.md` and `overview.md`, register the root. **Report only what you actually wrote.**
5. **None found, or none chosen, is a normal outcome.** Say the workspace is ready and name the next
   step. An empty workspace is not a failure.
6. **Reached from the automatic form, the directory you started in is already being registered** by
   its step 5 - do not offer it twice, and do not register it twice.

**This is not the scanning `/nk:init` refuses to do.** The banned behaviour is reading a user's
material and inferring a project map from it - that is `/nk:adopt`'s job, and it does not ship. This
enumerates directory entries
one level below a path the user just named, asks about each, and reads nothing inside any of them.
The offer is a proposal with a confirmation, exactly as the project name already is.

---

## Project - `<workspace>/.notekeeping/projects/<name>/`

**This is what stops a project from being guessed.** Without it, resolving a newly cloned repository
had to fall back to the directory's basename, which is how a project gets invented.

1. **Resolve the workspace store** per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. No store
   above this directory means **refuse** and point at a workspace init - do not create one silently.
2. **Find the repo root** with `git rev-parse --show-toplevel`, and handle three outcomes, not two:

   | What is true | What you record |
   |---|---|
   | a repository, git present | the repo root - resolution is automatic from any subdirectory after this |
   | **not** a repository, git present | no root; the project resolves by name |
   | **git unavailable, or you cannot run it** | **unknown.** Say so, and record no root |

   The third case is not the second. Recording *"no repository"* when you could not tell makes every
   later git-dependent behaviour report *not applicable* for a project that may well be a repo, and
   nothing will ever correct it.

   **It is not the first either, and that is the easier mistake to make.** When git cannot answer,
   do not substitute the filesystem: globbing for `.git/` and recording *"a repository, root here"*
   is an inference presented as a measurement, and what lands in the config is indistinguishable
   from a root git actually returned. **Record a repo root only from `git rev-parse --show-toplevel`.**
   `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md` already forbids inferring a *store* from the
   filesystem; the repo root is the same rule. *(Measured: with git unavailable, two runs out of two
   reported the root as confirmed.)*
3. **Propose the name and confirm it.** The repository's directory name is a *proposal*, not an
   answer - say which name you will use and let them correct it. **A project need not be a repo:** an
   initiative with no clone is a first-class case, and resolves by name.
4. **Create the directory, `NOTES.md` and `overview.md`.** Those are the two documents `/nk:project`
   owns. **Resolve both definitions per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`
   before writing either** - the user's overlay wins over the shipped
   `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/project/{NOTES.md,overview.md}`. Write the
   structure each definition specifies and nothing beyond it.

   **These two are not stubs, and the no-stub rule below does not reach them.** A register is a stub
   when empty because it looks answered; `NOTES.md` and `overview.md` are the documents that say
   where things are and what this project is, and a project without them has nowhere to point.
   **Create no registers and no ledger** - not `gotchas.md`, not `decisions.md`, not `domain.md`.
   A typical set invites stub files, and a stub looks answered. They appear when promotion first
   writes to them.

   **Report only what you actually wrote.** If a definition could not be resolved, say so and create
   nothing rather than describing a file that is not there.
5. **Register the repo root** so later sessions resolve without asking again.
6. **Write the repo projection**, per `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - the file,
   and the ignore entry it orders. **Registration is what creates a projection, not the first save**,
   so a project that has just been registered is already delivering. Skip it when
   `projections.enabled` is off, and say that you skipped it rather than reporting a file you did not
   write.

   **A repo that already holds a `CLAUDE.local.md` of the user's own is the ordinary case here**, not
   an edge: append below it, never modify a byte above it, and check for our markers *anywhere* in the
   file first. `projections.md` has the rule; this is the command that reaches it most often.

   **Only this project's file.** Registering one project never touches another project's projection,
   because the repo projection carries the project alone - when several are registered in one run,
   each gets its own write, and nothing is concatenated.

---

## Refuse a non-empty directory

If the target store directory exists and has any content, **stop**. Say:

> `<path>` is not empty - it has <what you saw>. `/nk:init` only creates an empty store, so it
> will not touch this one.

**Then stop.** Reading what is already there and proposing where it belongs is `/nk:adopt`, which
is a later tier and **is not implemented here** - so do not offer it as a next step the user can
take. Say the directory is not empty, say this command only creates empty stores, and name the two
things they can do now: point `/nk:init` at a different path, or move the existing content aside
themselves.

Do not create a store around material nobody has looked at. This is the one check `init` makes.

## Two rules for the config files

**Write no *setting* a store does not need**, and at init that means exactly one: `schema_version`,
in a workspace store config and in global's alike. Every other setting has a documented default, the
file is edited by hand, and a wall of commented settings makes it unreadable.

**The `## Projects` registry is not a setting, and this rule does not forbid it.** Project init
writes a project's `dirs:` there - that is step 5 above, and `06-config.md` shows the section in the
store config as the normal, populated state. *(Measured: read as a ban on the registry, `/nk:doctor`
reported a store as out of spec for holding the very mapping `/nk:init` had just written.)*

**Never invent a setting name.** Writing a plausible-looking key that the plugin does not read is
worse than omitting it: it looks configured. If you are not certain a key exists, leave it out.

## Report

State **every level you created**, the exact path of each, and every file written - including each
project registered by the offer. The automatic form routinely creates three at once, so report them
as a list rather than a sentence.

Then the single next step: `/nk:work` to start something, `/nk:init` inside a repository cloned
later to add it, or `/nk:help` to see what exists.

**Say how many calls are left.** After the automatic form there are none for what already exists -
global, the workspace and its repositories are done. Say so, because the impression this command
otherwise gives is that it must be run once per level forever.

**Name every write outside the store, separately.** Since a projection is written at registration,
this command no longer writes only inside `.notekeeping/`: each registered repo gets a
`CLAUDE.local.md` and a `.git/info/exclude` line, and the workspace root gets one too. **List them by
absolute path**, and say which were *appended to* rather than created. A user who agreed to register
four repositories has agreed to four files in their own trees, and the only moment they can see that
is this report.

Nothing else on disk was touched. Say so, and mean it literally - if you wrote anything not on that
list, name it instead.
