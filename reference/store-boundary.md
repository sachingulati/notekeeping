---
doc:   store-boundary
title: Resolving a store, and staying inside it
---

# The store boundary

**Resolve the store first, before reading or writing anything. Every read and every write stays
inside it.**

A **store** is a `.notekeeping/` directory. There are two kinds and no others:

| Store | Where | Its scope |
|---|---|---|
| **global** | `~/.notekeeping/` - a fixed path, always | your working life, not any codebase |
| **workspace** | `<workspace>/.notekeeping/` | the projects that sit in that workspace |

**A store's own scope sits at its root; every narrower scope is a subdirectory.** So a workspace
store holds `projects/<name>/` and `work/<bucket>/<id>/` beneath it, and its own files -
`NOTES.md`, `domain.md`, `gotchas.md`, `decisions.md` - sit directly in `.notekeeping/`. There is no
`global/` directory: `~/.notekeeping/` **is** global scope.

**Two files at a store's root are the plugin's own bookkeeping rather than knowledge**: `config.md`
and `index.md`, plus `baseline.md` where `/nk:doctor --baseline` has written one. They carry no
schema stamp, no definition governs them, and nothing that sweeps the store for knowledge files
treats them as one.

## Resolving it

1. **Walk up** from the working directory to the nearest `.notekeeping/`. That is the workspace
   store. **Test the exact path at each level** - does `<dir>/.notekeeping` exist - rather than
   listing a directory and reading what comes back. **A store is a hidden directory, and a listing
   is not a reliable witness to one:** a plain `ls` omits it, and a glob of `*` has both skipped
   hidden directories and descended into them, depending on the harness. A walk built on a listing
   can report *no store* while standing next to one.

   **The failure is silent, and so is the obvious fix.** Probe for the store **by name**; never
   ask a listing to reveal it:

   | Pattern | Finds a `.notekeeping/`? |
   |---|---|
   | `<dir>/.notekeeping/*` | **yes** - the literal name is in the pattern. **Use this one** |
   | `<dir>/**/<file>` | **yes** - a recursive descent crosses into hidden directories |
   | `<dir>/*` | **not dependably.** It has behaved both ways - and where it descends, it returns files from any depth, so a hit does not say which level holds the store |
   | `<dir>/.*` | **no.** The intuitive correction matches hidden files at that level, not what is inside a hidden directory, and reports nothing rather than erroring |
   | `<dir>/*/<file>` | **no - it can return nothing with the file present.** See *A pattern that matches nothing*, below |

   **So: `Glob` the exact path `<dir>/.notekeeping/*`, or `Read` a file you expect inside it.**
   Existence is proved by a hit, never by a name's absence from a listing. **A walk that returns
   "no store" after only listing directories has not looked**, and must not be reported as an
   absence.

   **The walk stops below `~`.** Under your home directory, the last level probed is the one
   directly beneath `~` - `~` itself is global's home, a workspace cannot sit there, and nothing
   above it is yours. Outside your home directory, walk to the filesystem root. **A probe that
   errors or times out is named as unprobed**, not counted as an absence and not reported as an
   outer store: say which levels could not be checked, and carry on with the nearest store found.
2. `~/.notekeeping/` is **global**, and is an ancestor of every workspace, so it is always in scope.
3. **Found nothing?** Stop and say so. Point at `/nk:init`. Do not continue with a guess.
   **Name the directories you actually walked.** *"No store above here"* is a claim about every
   ancestor, and it is worth exactly as much as the walk behind it - so make the walk visible and
   the claim checkable. **Do not report an absence you did not look for.**

4. **Found more than one below `~/`?** Workspaces do not nest. **Use the nearest**, and say that the
   outer one exists and is being ignored, naming both paths. Do not refuse - running against the
   obviously-intended store and saying so beats not running at all. `/nk:doctor` reports it as an
   error.

**Walking up is resolution, not discovery, and the difference is what you match on.** You are
looking for one exact name that the plugin creates and nothing else does. A `.notekeeping/` exists
only because someone ran `/nk:init` there, so finding one above you is finding a declaration, not
guessing from shape.

## Never infer a store from the filesystem

**A directory that looks like a notes store is not a store.** Do not scan the home directory, and do
not adopt a folder because it contains markdown, a `Tickets/` directory, a `notes/` directory, or
files shaped like notes. **Only an exact `.notekeeping/` counts.**

A person may keep several piles of notes; none of them is a store unless it has been initialised as
one, and the rest are none of this plugin's business.

**Resolving against the wrong pile is not a harmless error.** `load` only reads, but `save`
resolves a target the same way and `save` writes.

## Knowing that other stores exist

**Global's `config.md` carries a `## Workspaces` registry** - the absolute path of every workspace
store `/nk:init` has created on this machine. It is the same shape as a store config's `## Projects`,
written at creation for the same reason, and it is **not a setting**.

**It exists because this one thing cannot be derived.** The walk finds the store above you; there is
no walk that finds a store you are not standing under, and scanning for `.notekeeping/` is what the
section above forbids. A workspace that is not an ancestor of the working directory is otherwise
invisible for ever.

**It is for enumeration, and never for resolution.** *Which store am I in* is always the walk, on
every command, with no exception and no shortcut. The registry answers only *what else is there* -
naming a candidate a command may then offer to act on.

**A registry entry is a candidate, never a fact.** Before acting on one, prove the store is there the
way existence is always proved here: probe `<path>/.notekeeping/*` and take a hit. A directory can be
moved, deleted, renamed or sit on a volume that is not mounted, and **a path written down a year ago
is not evidence that anything is there now.**

**A stale entry is reported, never removed.** `/nk:doctor` names it; nothing deletes it, because an
absent path is as likely to be an unmounted disk as a deleted store, and the two are not
distinguishable from here.

## Staying inside it

| | |
|---|---|
| Reads | the resolved workspace store, plus `~/.notekeeping/`. Plus **git state** from the repository you are working in, **and from the repositories the store registers** where a command reports across projects - `init`, `load` and `project` do. **Address another repository as `git -C <repo> <subcommand>`**, never `cd <repo> && git ...`: every command that reads git declares `Bash(git -C:*)` so that form is granted, and it is the one form that is. Use it only for the read-only subcommands the command already declares. **Where a git read is refused anyway, say so in the report** - *not checked* is a different answer from *found nothing* - and for a repo's own `CLAUDE.md` or `README.md` as a *source* when assembling `overview.md`, never as a target |
| Writes | **inside a store, plus the seven named targets below - and that list is closed. Every one of them is personal and local; none is a file anyone else reads.** The machine config is `~/.notekeeping/config.md`, which is a store |
| Grep and glob | rooted at a resolved store - never at `~`, never at the working directory. **One exception, and it is the drain's**: `/nk:save` globs `~/.claude/projects/*/memory/` to find the store it is draining, which is the only way to address a directory keyed by a slug. It is a listing, not a search of `~` |
| A path in a query | resolved relative to the store, and refused if it escapes it |

### A pattern that matches nothing

**A `*` segment followed by a literal filename can return zero with the file present.**
`<store>/projects/*/overview.md` has been measured returning nothing against a store holding that
file, while `<store>/**/overview.md` found it. A pattern that **ends** in a wildcard -
`<dir>/*/memory/*.md` - did not fail the same way. Nothing tells you which one you ran: the empty
result is indistinguishable from a real absence.

- **To find a named file at any depth, root the pattern at the store and lead with `**`** -
  `<store>/**/overview.md` - then keep the hits whose path has the shape you wanted. Never put a
  single `*` segment in front of a literal name.
- **To address one exact file, `Read` it.** Globbing is for files whose names you do not know.
- **A zero is a finding until a second form agrees.** Before reporting that a definition has no
  files, or that a thing is absent, re-run it as `<store>/**/<name>`. Two forms returning zero is
  an absence; one is not.

**Everything outside `.notekeeping/` is the user's.** Repositories, loose files, personal scratch
and anything else are read only when the user names them, and never reorganised or rewritten.

### The seven writes that land outside a store

**The property this protects is not *never outside a store* - it is *only into a target somebody had
to name*.** They are listed here rather than carved out per command, because a rule that is quietly
false is one the next reader is entitled to ignore.

**None of the seven is committed, and none is read by anybody but you.** Three are files the plugin
generates and owns; two are single entries added to files of yours (`.git/info/exclude`, `~/.claude/settings.json`); one removes content that is **not at `HEAD`**, so even there nothing a
teammate can see is touched; the drain is below the table, because what bounds it is not a path.

| What | Target, and what bounds it | Written by |
|---|---|---|
| the repo projection | `<repo>/CLAUDE.local.md` - only a repository **registered as a project** | `init`, `save`, `project`, `adopt`, `upgrade`, `doctor --fix` |
| the workspace projection | `<workspace-root>/CLAUDE.local.md` - only the directory holding that store's `.notekeeping/` | the same, less `project` - which writes a named project's and nothing else |
| the global projection | `~/CLAUDE.local.md` - only the home directory, beside `~/.notekeeping/` | the same as the workspace's |
| the ignore step | `<repo>/.git/info/exclude` - never `.gitignore`, and written **before** the projection | the repo projection's six |
| the read permission | `~/.claude/settings.json` - **only adding** the workspace root and `~/.notekeeping` to `permissions.additionalDirectories`. Below | `init`, and `doctor`'s repair |
| the context-file trim | a `CLAUDE.md` or `CLAUDE.local.md` - **only lines not at `HEAD`, only once their content is in the store**, and only on the yes to a proposal that showed every line (5.75) | `adopt` |

**The trim is one of two writes that remove rather than generate - the drain, below, is the other -**
which is why it is bounded twice
over: by `HEAD`, and by the content already existing somewhere else.
`${CLAUDE_PLUGIN_ROOT}/commands/adopt.md` holds the invariant.

**`init` is on that list because creation belongs to registration** - a registered project is
delivering before any save has run. **`adopt` refreshes the ones it wrote into, `doctor --fix`
rebuilds them and `/nk:upgrade` regenerates them after a migration**; all of them write the same three targets under the same rules, which is why they
are in the column rather than carved out beside it. Which command owns which *moment* is
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`; if that file and this one ever disagree about which
command writes a projection when, that file is the one to believe.

**And the drain, which removes rather than generates:** `/nk:save` drains the harness memory store,
removing **only what it has just promoted into a store** and nothing else. The bound is the content
existing somewhere else, exactly as the context-file trim's is - not who wrote the item, which
nothing records and nothing can infer. **An item that was not promoted is left alone and reported.**

**It is on this list because it changes the filesystem outside a store**, which is the only test the
list applies. It was once described here as *not a write to disk*, which kept the count at four and
kept the one operation that **deletes the user's material** off the list that exists to bound
exactly that. The count is the cheap thing; the bound is the point.

**That store is not inside any store of ours, and it is keyed per working directory** -
`<harness home>/projects/<cwd-slug>/memory/`. So a drain reaches the one directory the save ran in,
which is why `/nk:doctor` reports the sibling stores a project's directories resolve to and the last
save did not see.

### The read permission

**Without it the plugin does not work**, so creating a store adds it. Every `Read on demand` line
points above the repository by absolute path, and Claude Code reads outside the working directory
only where `permissions.additionalDirectories` allows it; missing, the always-loaded half arrives
and every on-demand read is refused.

**Add, never replace.** Read `~/.claude/settings.json`, parse it, and add each missing path to
`permissions.additionalDirectories` - creating the key, or the file, only if absent. Every other key,
every other entry and the order of what is there stay exactly as they were. **Write absolute paths**,
forward slashes, the same form the projections use.

**If the file does not parse, write nothing to it.** Report that, and print the one line to add by
hand - a settings file the plugin cannot read is one it must not rewrite.

**Only the user's own settings, never a project's.** `.claude/settings.json` in a repository is
committed and the team's; this entry is personal, like every other write on this list.

### The one thing that can leave this machine

**A report, or a summary, can be published as a page** - `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`. It is
not a write to disk and it is not on the table above, and it is named here because it is the only
thing the plugin does that leaves the machine at all.

**Four properties bound it, and they are the reason it is allowed:** it happens **only on an
explicit yes** in the run that offers it; it carries **only what that run already printed** to the
terminal; it **never writes anything**, in a store or out of one; and **the terminal report stands
whether it happens or not**, so nothing depends on it. **A page published without being asked for is
a defect**, exactly as a write outside a store is.

**Nothing else, ever. A write outside a store that is not on this list is a defect, not a judgment
call.** What each of the three projections may contain is governed by
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, and the trim's bound is
`${CLAUDE_PLUGIN_ROOT}/commands/adopt.md`'s; this table settles only *which targets exist*, so
that *inside a store* stays checkable rather than approximately true.

## Every run

**Nothing changed, nothing written.** A command whose output would be byte-identical to what is on
disk writes nothing and says so - *nothing new since the last save*. Compare the rendered file to the
file, never the sources to their last generation. A second `/nk:save` straight after the first leaves
`resume.md` and `session.md` untouched: an empty diff and a fresh date that say nothing new are
churn, for a person reading git history as much as for a tool calling repeatedly. Under `--oneline`
this is the `no-change` status (`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`).

**Check before touching the filesystem, in one order: the flags, then the store, then the project.**
Where a run cannot go on, **which check fires first decides what the user fixes first** - so the
cheapest, most actionable one goes first: a flag the user controls, before a missing store, before
an unresolvable project.

## Resolving a project inside the store

**Never invent a project.** A project exists because `/nk:init` created it.

1. Resolve the working directory to a repo root with `git rev-parse --show-toplevel`, **where git
   can answer**.
2. Match that root against the projects registered in the store.
3. **No match → refuse and point at `/nk:init`.** Never fall back to the directory's basename.

**Git has three outcomes, not two**, and the third must not be reported as the second:

| What is true | What you record |
|---|---|
| a repository, git present | the repo root |
| **not** a repository, git present | no root; resolve by name, and git-dependent behaviour is *not applicable* |
| **git unavailable, or you cannot run it** | **unknown.** Say so, and record no root |

**Directories, listings and sizes come from `Glob` and `Read`, never from a shell** - no `ls`, no
`wc`, no `mkdir`. **`git` is the only shell this plugin ever calls**, and the row above is what makes
even that optional, so the plugin behaves the same on Windows, macOS and Linux with or without a
POSIX shell. `Write` creates the directories it needs on the way to a file.

*"There is no repository here"* and *"I could not determine whether there is one"* are different
claims. Recording the first when the third is true is unfalsifiable afterwards: every later
git-dependent behaviour reports *not applicable* for a project that may well be a repository.

**And *"the root is here"* is the same error mirrored.** When git cannot answer, do not fall back to
the filesystem - a `.git/` found by glob is an inference, and once written it is indistinguishable
from a root `git rev-parse` returned. **A repo root is recorded only from
`git rev-parse --show-toplevel`; otherwise it is unknown.** This is the rule above applied to the
project instead of the store: *never infer from the filesystem what a declaration is supposed to
tell you.*
