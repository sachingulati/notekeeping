---
name: init
description: Create a Notekeeping store - global, a workspace, or a project in one - and register its repositories. Use when the user asks to set up Notekeeping, or register a folder or project with it.
argument-hint: "[path] [--workspace] [--project]"
allowed-tools: Read, Glob, Edit, Write
---

Create a store - global or workspace - or register a project inside one. It creates structure -
a config, the workspace's `NOTES.md`, and, for a project, the two documents it needs in order to
exist. It
never mines your material for content. What it needs from a repository it asks git, per
`${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs. **Then the schema release**: read each resolved store's `schema_version` and `Grep` the release in `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (*The schema release this plugin ships*). A store at it is not mentioned; one behind it is said in one line on every run, naming `/nk:upgrade`, and the run carries on; newer or absent follows that file's four answers. No store yet is
   this skill's normal case - it is the one that creates them.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:init`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

What it reads to fill `overview.md`'s fields - facts about what the project *is* - is the
repository's own orientation sources: a build file, a `README.md`, the repo's own `CLAUDE.md`.
Nothing read for that becomes an entry in a register. It also reads `~/.claude/settings.json`, the
workspace and global registries, and any `CLAUDE.local.md` already present, to decide what needs
creating and what is already there - and whether a repository it registers has an `AGENTS.md` the
read line would hide.

**It does not guess a project map, and it reads no notes.** A notes folder, a wiki export, another
tool's store, the knowledge sitting in a `CLAUDE.md`: none of it is read here. Bringing that material
in is `/nk:adopt`'s job.

## The three forms

| Form | Does |
|---|---|
| `/nk:init [path]` | stops where the path is already set up, creates global at `$HOME`, and otherwise asks *workspace or project*. Below |
| `/nk:init --workspace [path]` | create the workspace store, and stop there - plus the offer of the repositories under it |
| `/nk:init --project [path]` | create the project inside the workspace above it, and stop there |

`[path]` defaults to the working directory in every form.

Each level has its own reference; read one only when this run reaches that level:

| Level | Follow |
|---|---|
| **global** | `${CLAUDE_PLUGIN_ROOT}/skills/init/references/global.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` and `${CLAUDE_PLUGIN_ROOT}/reference/projections-writing.md`) |
| **a workspace**, and the offer of its repositories | `${CLAUDE_PLUGIN_ROOT}/skills/init/references/workspace.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, `${CLAUDE_PLUGIN_ROOT}/reference/projections-writing.md` and `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`) |
| **a project** | `${CLAUDE_PLUGIN_ROOT}/skills/init/references/project.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`, `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`, `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`, `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, the shipped `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/project/NOTES.md` and `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/project/overview.md`, `${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`, `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` and `${CLAUDE_PLUGIN_ROOT}/reference/projections-writing.md`) |

Detecting is not guessing: every level except global confirms before it writes - global's path is
fixed - and the report names everything written.

---

## `/nk:init` - the bare form

**Read the path first: where it is already set up, say so and stop, writing nothing.** The first
row that holds decides:

| The path | Do |
|---|---|
| is `$HOME` - compared per the store-resolution rule, the home path taken from a tool's answer per its *A ~ path* | **global.** Missing: create it, per the global reference, and report it. There: say global already exists at `~/.notekeeping/`, and stop |
| resolves to a project by its read line alone - a folder moved, or another clone - per the store-resolution rule's *Resolving a project inside the store*, resolved from the path | **create global if it is missing**, then the outward walk, unasked: the project reference's step 3 decides it |
| resolves to a project by a `dirs:` entry, the same way | say which project it is already registered as - *inside* it, where the path lies below the project's folder - and stop |
| holds a workspace store - `<path>/.notekeeping/config.md` reads | say it is already a workspace, by path, and stop |
| anything else | **create global if it is missing**, then ask one question: *Workspace / Project* |

**Global is written before the question, even one nobody can answer**: no answer changes it, so
holding it back only leaves it unwritten. **The question is asked alone** - everything after it
depends on the answer.

- **Workspace** runs the `--workspace` form at the path, as if it had been typed.
- **Project** runs the outward walk below.

### Project - the outward walk

Work outward from the path to the workspace that will hold it.

1. **Global** is already in place - written before the walk began.
2. **Decide the project directory.** Find the repo root with git, as the project reference's step 2
   does: a repository → that root is the project. Not a repository → the path itself is the
   project; the answer *Project* already said so.
3. **Resolve the workspace before creating the project.** A project cannot exist without one -
   it lives at `<workspace>/.notekeeping/projects/<name>/`, so there is nowhere to put it otherwise.
   Walk up per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - **starting at the working
   directory itself**, not its parent:

   | What the walk finds | Do |
   |---|---|
   | a workspace store already, here or above | use it. Nothing to ask |
   | no workspace, **and the parent is `$HOME`** | **refuse** - see below |
   | no workspace, parent is anything else | propose the parent as the workspace and ask |

4. **Create the workspace if one was agreed** - including the offer of the other repositories
   under it. The sibling clones are found at the same moment and there is no reason to make the
   user come back for them.
5. **Create the project.**

Run it again later and the same path stops at once, naming what is already there. Inside a
repository cloned later under a workspace that already exists, answering *Project* finds steps 1,
3 and 4 done and simply registers it. That is how a repository cloned later is added, with no flag.

### `$HOME` cannot be a workspace

A workspace store at `$HOME` would be `~/.notekeeping/` - global's fixed home - collapsing the
two scopes into one file set. So whenever the workspace would be `$HOME` - the parent in the
outward walk, or the path `--workspace` names or defaults to - **stop and say why**:

> A workspace at your home directory would be the same folder as the global store. Put your
> repositories under a directory - `~/projects`, say - and run `/nk:init` there, or name one with
> `/nk:init --workspace <path>`.

Do not offer to continue without a workspace, and do not create the project at global scope. Global
holds knowledge that is not about any codebase; a project there is a category error, not a
shortcut.

---

## Refuse a non-empty directory

This is a check on a store being created, and only there. A store that already exists is the
ordinary re-run above - step 1, 3 or 4 finding its work done - and it is never refused for holding
the notes it is supposed to hold.

If the target store directory is one this run would create and it exists with any content,
**stop**. Say:

> `<path>` is not empty - it has <what you saw>. `/nk:init` only creates an empty store, so it
> will not touch this one.

**Then stop and name `/nk:adopt`** - reading what is already there and building the store from it
is that skill's job. The other two ways out are pointing `/nk:init` at a different path, or moving
the existing content aside.

Do not create a store around material nobody has looked at.

## The config files

**Write exactly one setting: `schema_version`** - in a workspace store config and in global's alike.
Take its value from `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`, where the shipped
version is stated; a number typed into this file drifts the first time the schema moves.
Every other setting has a documented default and is set with `/nk:config`; a wall of commented settings is
unreadable. **Never invent a setting name**: a plausible key the plugin does not read looks
configured.

The `## Projects` registry is not a setting. Project init writes a project's `dirs:` there, and
a store config may hold that mapping.

Nor is global's `## Workspaces` registry. Creating a workspace store appends its absolute path
there, in `~/.notekeeping/config.md`. On a fresh machine `--workspace` reaches this step with no
file to append to, so creating global first, where it is missing, rule file and all, is part
of the same step - that is what lets a machine's workspaces be enumerated at all. What the registry
is and is not for is `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, *Knowing that other stores exist*. **Append; never rewrite the
list**, and do not add an entry for a workspace that already has one. This is the only thing
workspace creation writes into global.

## The read permission

**Creating global or a workspace adds `~/.notekeeping` or the workspace root (the directory holding
`.notekeeping/`) to `permissions.additionalDirectories` in
`~/.claude/settings.json`**, and with it the other entries *The read permission* lists, per
`${CLAUDE_PLUGIN_ROOT}/reference/store/writes.md` - add, never replace, and nothing written if the file does not parse. Without it every
on-demand read of the store is refused, so this is part of creating the store, not an option on it.

## Asking

**Every question this skill asks follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`** - `nk:init
needs:` and the open questions. Its questions: whether the bare form's path is a workspace or a project, the workspace path (proposed, or named),
which of the offered repositories to register, and each project's name - plus, once the report is out, the adopt offer and the idle-compaction question under `## Report`. **After the
workspace-or-project question, ask everything open at once** - in the outward walk the workspace, the offer and the name are usually known
together - and nothing the conversation already answers.

**Each write outside the store may also be asked about by the harness** - a read line, an exclude
entry, the rule file, the settings entry. A refusal there is that file not written: report it as
such, per the projection-writing rule's *Failing*, never as done.

## Report

State every level you created, the exact path of each, and every file written - including each
project registered by the offer. The outward walk routinely creates three at once, so report them
as a list rather than a sentence.

Then the single next step. **A run that created a store or registered a project names
`/nk:adopt`**, run from the workspace and then from each registered project that has its own
`CLAUDE.md`: it brings in what your `CLAUDE.md` files and notes already hold, into this workspace's
store and global, and run again until it says nothing is left. A run reads the `CLAUDE.md` files its
folder loads, so a project's own is read only from inside that project, and each workspace is adopted
from inside it. Otherwise: `/nk:work` to start something, `/nk:init`
inside a repository cloned later to add it, or `/nk:help` to see what exists.

**After `--workspace` or `--project` - typed, or picked as *Workspace* - created its store or registered its project, offer to adopt
that path now** - one question, after the report, with `AskUserQuestion`: *Adopt `<path>` now /
Later*. The question says, in one line each, that adopt can use a lot of usage depending on how much
it reads, and that `/nk:adopt <path>` can be run at any time. **Adopt `<path>` now** starts the
`Skill` call `nk:adopt` with `<path>` - the path this run was given, or the working directory where
it was given none - and nothing else; **Later** ends the run. A re-entry whose only open question is
this one makes that call, or ends, and repeats nothing else.

**A run that created a store, where `~/.claude/settings.json` does not set `idleCompaction`, also
asks whether to turn idle compaction off** - in the same `AskUserQuestion` as the adopt offer, or
alone where there is none: *Turn it off / Keep it on*, with what each costs, per
`${CLAUDE_PLUGIN_ROOT}/reference/store/writes.md` (*Idle compaction*). **Turn it off** adds the key
by that rule, before any adopt call, and names the write; **Keep it on** writes nothing.

Say which `/nk:init` runs are still needed. After the outward walk there are none for what
already exists - global, the workspace and its repositories are done - so the user does not think
this runs once per level forever.

**Name every write outside the store, separately.** A read line is written at registration, so each
registered repo gets a `CLAUDE.local.md` and a `.git/info/exclude` line, the workspace root gets
a `CLAUDE.local.md` too, and `~/.claude/rules/notekeeping.md` is written when this run created global. The settings entry is
listed too: the file, and each path added or already present - and `idleCompaction`, where the
answer added it. List them by absolute
path, and say which were *appended to* rather than created. This
report is the only moment the user sees the files they just agreed to in their own trees.

Nothing else on disk was touched. Say so literally: anything written that is not on that list is
named instead.
