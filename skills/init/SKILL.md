---
name: init
description: Create a Notekeeping store - global, a workspace, or a project in one - and register its repositories. Use when the user asks to set up Notekeeping, or register a folder or project with it.
argument-hint: "[path] [--workspace] [--project] [--oneline]"
allowed-tools: Read, Glob, Edit, Write
---

Create a store - global or workspace - or register a project inside one. It creates structure -
a config, the workspace's `NOTES.md`, and, for a project, the two documents it needs in order to
exist. It
never mines your material for content. **This skill runs no shell and no git**: what it needs from
a repository it reads from `.git` with the file tools.

Before anything else, in order:
1. **`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
2. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`. No store yet is
   this skill's normal case - it is the one that creates them.
3. **An overlay?** If `<store>/schema/skills/init/` exists, follow `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`: a `SKILL.md` there replaces the rest of this file, and a file under its `references/` replaces the shipped reference of that name wherever this skill cites it.
4. **Infer before asking**: read what the conversation already states, and ask only what is still open.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:init`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

What it reads to fill `overview.md`'s fields - facts about what the project *is* - is the
repository's own orientation sources: a build file, a `README.md`, the repo's own `CLAUDE.md`.
Nothing read for that becomes an entry in a register. It also reads `~/.claude/settings.json`, the
workspace and global registries, and any `CLAUDE.local.md` already present, to decide what needs
creating and what is already there.

**It does not guess a project map, and it reads no notes.** A notes folder, a wiki export, another
tool's store, the knowledge sitting in a `CLAUDE.md`: none of it is read here. Bringing that material
in is `/nk:adopt`'s job. This skill is not a substitute for it and must not improvise one.

## The three forms

| Form | Does |
|---|---|
| `/nk:init [path]` | the whole setup, working outward from where you are. Below |
| `/nk:init --workspace [path]` | create the workspace store, and stop there - plus the offer of the repositories under it |
| `/nk:init --project [path]` | create the project inside the workspace above it, and stop there |

`[path]` defaults to the working directory in every form.

Each level has its own reference; read one only when this run reaches that level:

| Level | Follow |
|---|---|
| **global** | `${CLAUDE_PLUGIN_ROOT}/skills/init/references/global.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`) |
| **a workspace**, and the offer of its repositories | `${CLAUDE_PLUGIN_ROOT}/skills/init/references/workspace.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` and `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`) |
| **a project** | `${CLAUDE_PLUGIN_ROOT}/skills/init/references/project.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`, `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`, `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, the shipped `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/project/NOTES.md` and `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/project/overview.md`, `${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md` and `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`) |

Detecting is not guessing: every level except global confirms before it writes - global's path is
fixed - and the report names everything written.

---

## `/nk:init` - the automatic form

Work outward from the repository you are standing in, rather than asking for an abstraction
first.

1. **Create global if it is missing** - `~/.notekeeping/`, a fixed home, no judgment, nothing to ask.
   **It is written before any question this run asks, even one nobody can answer**: no answer
   changes it, so holding it back only leaves it unwritten.
2. **Decide the project directory.** Find the repo root by reading, as the project reference's step 2
   does: a repository → that root is the project. No `.git` found → ask whether to treat this
   directory as a project, and stop if the answer is no.
3. **Resolve the workspace before creating the project.** A project cannot exist without one -
   it lives at `<workspace>/.notekeeping/projects/<name>/`, so there is nowhere to put it otherwise.
   Walk up per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`:

   | What is above | Do |
   |---|---|
   | a workspace store already | use it. Nothing to ask |
   | no workspace, **and the parent is `$HOME`** | **refuse** - see below |
   | no workspace, parent is anything else | propose the parent as the workspace and ask |

4. **Create the workspace if one was agreed** - including the offer of the other repositories
   under it. The sibling clones are found at the same moment and there is no reason to make the
   user come back for them.
5. **Create the project.**

Run it again later and the same path does less. Inside a repository under a workspace that
already exists, steps 1, 3 and 4 all find their work done and it simply registers the project. That
is the *"once per repository cloned later"* case, and it needs no flag.

### `$HOME` cannot be a workspace

A workspace store at `$HOME` would be `~/.notekeeping/` - global's fixed home - collapsing the
two scopes into one file set. So whenever the workspace would be `$HOME` - the parent in the
automatic form, or the path `--workspace` names or defaults to - **stop and say why**:

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
Take its value from `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md` and `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`), where the shipped
version is stated; a number typed into this file drifts the first time the schema moves.
Every other setting has a documented default and is edited by hand; a wall of commented settings is
unreadable. **Never invent a setting name**: a plausible key the plugin does not read looks
configured.

The `## Projects` registry is not a setting. Project init writes a project's `dirs:` there, and
a store config may hold that mapping.

Nor is global's `## Workspaces` registry. Creating a workspace store appends its absolute path
there, in `~/.notekeeping/config.md`. On a fresh machine `--workspace` reaches this step with no
file to append to, so creating global first, where it is missing, rule file and all, is part
of the same step - that is what lets a machine's workspaces be enumerated at all. What the registry
is and is not for is `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`. **Append; never rewrite the
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
needs:` and the open questions - with `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` when nobody can
answer. Its questions: the workspace path (proposed, or named), whether a directory with no `.git` is
a project, which of the offered repositories to register, and each project's name. **Ask everything
open at once** - in the automatic form the workspace, the offer and the name are usually known
together - and nothing the conversation already answers.

**Each write outside the store may also be asked about by the harness** - a read line, an exclude
entry, the rule file, the settings entry. A refusal there is that file not written: report it as
such, per the projections rule's *Failing*, never as done.

## Report

State every level you created, the exact path of each, and every file written - including each
project registered by the offer. The automatic form routinely creates three at once, so report them
as a list rather than a sentence.

Then the single next step: `/nk:work` to start something, `/nk:init` inside a repository cloned
later to add it, or `/nk:help` to see what exists.

Say which `/nk:init` runs are still needed. After the automatic form there are none for what
already exists - global, the workspace and its repositories are done - so the user does not think
this runs once per level forever.

**Name every write outside the store, separately.** A read line is written at registration, so each
registered repo gets a `CLAUDE.local.md` and a `.git/info/exclude` line, the workspace root gets
a `CLAUDE.local.md` too, and `~/.claude/rules/notekeeping.md` is written when this run created global. The settings entry is
listed too: the file, and each path added or already present. List them by absolute
path, and say which were *appended to* rather than created. This
report is the only moment the user sees the files they just agreed to in their own trees.

Nothing else on disk was touched. Say so literally: anything written that is not on that list is
named instead.
