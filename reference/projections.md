---
doc:   projections
title: Writing a projection, and never clobbering what you did not create
---

# Projections

**A projection is a copy of store content, generated into a file the harness loads by itself.** It
is never a source. Losing every projection loses nothing - `/nk:doctor --fix` rebuilds them all.

**Both targets are `CLAUDE.local.md`. Never write a file named `CLAUDE.md`, at any level.** That
file belongs to somebody at every level it appears - the team at a repo root, the user everywhere
else - and `CLAUDE.local.md` is the platform's own slot for uncommitted personal content. **No
projection is ever written to a `CLAUDE.md`, at any level.** The one command that may edit one is
`/nk:adopt`, which removes uncommitted lines whose content it has already written into the store,
after its own confirmation - and never a byte that is at `HEAD`.

| Projection | Target | Source | Delivers |
|---|---|---|---|
| **repo** | `<repo>/CLAUDE.local.md` | `<store>/projects/<p>/NOTES.md` **and that project's `instructions.md`** | project |
| **workspace** | `<workspace-root>/CLAUDE.local.md` | the workspace store's own `NOTES.md` **and its `instructions.md`** | workspace |

**The repo projection carries the project and nothing else, always.** It does not walk on to the
workspace, because the workspace has a projection of its own and both files load in the same session
- so carrying it in both delivers every workspace fact twice, in the one place where bytes are
charged on every prompt. **Deliver each scope exactly once.**

## Five commands write a projection, and each owns a different moment

| Command | When | What it writes |
|---|---|---|
| **`/nk:init`** | **registration** - a project is added, or a workspace created | that project's projection, or the workspace one. **This is what creates a projection**; a registered project is delivering before any save has run |
| **`/nk:save`** | every checkpoint | the **active work item's project**, and the workspace projection. No other project, ever - it carries the project alone, so no other project's content can have changed |
| **`/nk:project`** | a rebuild | **the named project's, and only when a name is given.** Bare mode reports and writes nothing. **The repair path** for one project, and the only thing that picks up a newly added area |
| **`/nk:upgrade`** | after a migration | **both**, regenerated rather than migrated - a projection is derived, so it is rebuilt from the store it was just converted from, never converted in place |

**`/nk:doctor` writes one only under `--fix`**, and reports otherwise. That is what rebuilds **every**
projection in one run; `/nk:project` repairs them one at a time.

**Existence is registration's job; freshness is the save's.** Keeping those apart is what gives
`doctor`'s missing-projection finding a remedy: a registered project is delivering before any save
has run, and `/nk:project <name>` rebuilds a projection that has gone missing.

`<workspace-root>` is **the directory holding that store's `.notekeeping/`** - the same walk that
resolved the store already produced it. It is never configured and never guessed.

**Global has no projection.** `~/.notekeeping/` is an ancestor of every workspace, so delivering it
would mean `~/CLAUDE.local.md`, charged in every session on the machine. Global is read on demand.

## Before writing anything

1. **Registration is the switch.** A registered project is delivered, always; a repository nobody
   registered gets no projection.
2. **Is there anything to render?** The workspace projection is written whenever its source has
   content, whatever the project count. If `<store>/NOTES.md` is absent or empty there is nothing to
   render and no file appears - an empty render, not a failure. **The one exception is the moment
   the workspace is created**, where `/nk:init` writes the file empty on purpose: the markers are
   what every later save updates in place, and they have to exist before there is anything to put
   between them.
3. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and the file
   definitions per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`.

## The no-clobber rule

**Check the target before every write.** There are three cases and no others:

| The target | Do |
|---|---|
| **does not exist** | Create it. The whole file is the plugin's; the markers are just a generation boundary |
| **exists and has the markers** | Rewrite the generation header and what is between the markers. Everything else is preserved verbatim |
| **exists without them** | **Append the block at the end.** Never modify a byte above it |

**Detection keys on the markers appearing anywhere in the file - never on the file starting with the
generation header.** Once a block has been appended, that header no longer sits at the top, so a
top-of-file test fails to recognise a block already written and appends a second one on every save.
Search the whole file for `notes:begin`; that, and only that, decides between case 2 and case 3.

**Case 2 rewrites the block every time. Never compare the source to its last generation and
skip.** The block is rendered from the project directory as well as from `NOTES.md` - the
`Read on demand` line *is* the directory listing - so a source-file comparison misses a register
appearing or disappearing, and the projection under-delivers from then on. **Compare the rendered
block to what is between the markers**, which is both idempotent and correct: unchanged inputs
write nothing, and any input change lands.

**The third case appends rather than refusing, and the rule it protects is unchanged: never clobber
a file this plugin did not create.** Some people hand-write a `CLAUDE.local.md`; some other tool may
create one. Neither is the plugin's to *overwrite*, and refusing outright would deliver no notes at
all in that repository, which is the one thing the projection exists to do. Appending keeps both:
**every byte above the block is untouched**, and the block converts the file into an ordinary case-2
target from the next save onward.

**The notice goes in the header comment, not the body.** A projection is always-loaded content
charged on every prompt, so the body carries facts and nothing else; the header is where
*do not edit* already lives and is what a person editing the file actually reads:

```
<!-- GENERATED by the Notekeeping plugin from <source> - <date>
     Do not edit or delete this block; edits are overwritten. To get it back after deleting
     it, run /nk:project <name> for a repo projection, or /nk:doctor --fix for either.
     Everything above it is yours and is never modified. It is rewritten for as long as
     this project is registered. -->
```

**This is the header, stated once.** It is written the same way in both targets, with `<name>`
omitted where there is no project to name. **The rewrite covers the header as well as the block** -
it carries the source and the date, so leaving it in place while the block changes underneath it
publishes a generation date that is no longer true.

**A repo in this case still gets the ignore step.** The file now carries generated content, so it is
ignored exactly as one created here - see below.

## The ignore step, and its ordering

**Add one line to `.git/info/exclude`. That is the whole ignore step - there is no `.gitignore`
line and no setting that adds one**. `.gitignore` is tracked and team-owned; the projection is not
the team's file. The exclude is local, untracked and immediate, which are the projected file's own
properties.

**Read the file, and append only if the entry is absent.** That file is the user's own, it usually
already holds their local excludes and git's shipped comment header, and **a write that replaces it
destroys all of them**. Appending without the check is the other half of the same defect: the entry
accumulates a copy per save. One read, one match, at most one appended line.

**Ordering is load-bearing:** the `.git/info/exclude` entry **first**, and **then** write the file.
Creating the file first leaves a personal file untracked in a team repo, which is how stray files get
committed by somebody else's `git add -A`.

**The rule keys on the target, not on which projection it is.** Apply it whenever the directory
being written to is inside a git work tree; skip it otherwise. A workspace root is usually not a
repo and needs nothing. A workspace root that *is* a repo - a monorepo - gets exactly the same
treatment as any other repo.

**Nothing here is committable, which is the point.** `.git/info/exclude` is not tracked, so there is
no diff to raise, nothing for doctor to chase, and no state where an ignore is written but not yet
committed. A team that wants `CLAUDE.local.md` in its `.gitignore` adds the line itself; the plugin
never commits, so writing it for them would have saved nobody anything.

## What goes in

```markdown
<!-- GENERATED by the Notekeeping plugin from /abs/path/to/ws/.notekeeping/projects/repo-a/
     NOTES.md and instructions.md - <date> - the header above, in full -->
<!-- notes:begin -->

# repo-a - local (not committed)

## Always needed here
The facts you need on most tasks here

## Standing instructions
- **Do X, not Y.** The condition under which it applies.
- **When the work touches <topic> - <the cues that identify it> - read
  /abs/path/to/ws/.notekeeping/projects/repo-a/areas/<topic>/ first.**

## Read on demand - /abs/path/to/ws/.notekeeping/projects/repo-a/
gotchas - patterns - decisions - domain - runbook - architecture - interfaces

### Areas
- **<topic>** - the cues that identify it -> areas/<topic>/

Work in flight is not projected. Run `/nk:load` to restore it.

<!-- notes:end -->
```

**Two files, one scope - and the invariant is the scope, not the count.** The repo projection
renders from that project's `NOTES.md` and that project's `instructions.md`, and from nothing else;
the workspace projection renders from the store root's two. **A source from another scope is the
defect** - that is what delivers a fact twice in one session, which is the thing
*deliver each scope exactly once* forbids. The header names both files it read.

**`## Standing instructions` renders only where there is something to render.** An empty or absent
`instructions.md` produces no heading - an empty render, not a failure, exactly as an empty
`NOTES.md` produces no file.

**The heading is a literal, and detection keys on it.** `## Standing instructions`, spelled exactly
that way in both targets: it is what tells a reader which half is obeyed, and what
`/nk:doctor` greps to decide whether a scope's instructions reached the block at all. **Never by
reading the file** - the idiom the stamp checks already use. The same holds for `### Areas`, which
is what makes the rendered catalogue checkable against the directory listing that produced it. A
renamed heading is a silent delivery failure: the block still looks complete, and nothing that
looks for it finds it.

**The render carries the imperative and its condition, and drops `Retire when:` and the
provenance.** Both are real parts of the entry and neither is wanted on every prompt: the trigger is
what `doctor` greps in the store, and the source and date are what someone deciding whether to keep
an entry reads there. **A projection charged on every prompt carries what is to be obeyed and
nothing else** - the same reason the *do not edit* notice sits in the header rather than the body.

**The two halves are kept visibly apart, because they are read differently.** What is under
`## Always needed here` is true and may be wrong; what is under `## Standing instructions` is to be
obeyed. Merging them costs both: a fact stated as a command is unfalsifiable, and an instruction
stated as a fact is optional.

**Every path written into a projection is absolute.** Not store-relative, not repo-relative. The
projection lives in the repo and the store lives above it, so a path like
`.notekeeping/projects/repo-a/` resolves to nothing from where the file sits. **This covers paths
written inside an instruction**, which is where it is easiest to miss: the entry is authored short
in the store, and the render is what writes it in full.

**This matters most for the `Read on demand` line, which is the whole on-demand half of delivery.**
It is an instruction that causes a read; a path that does not resolve produces the worst failure
shape there is - the instruction is followed, the read is refused, and the model improvises, which
is worse than an error because the output looks like it had notes behind it.

**The path is absolute because the store sits above the repository, which is also why the store root
has to be in `additionalDirectories`.** That setting is what makes the read succeed rather than the
path that is written; `/nk:doctor` checks it, and `README.md` has the line.
The header's path is merely documentation, but it is written the same way for the same reason.

## What the on-demand half renders

**One line per area, never a bare `areas/`.** Naming the container says only that shelves exist and
leaves choosing between them to relevance recall - and relevance recall is the mechanism measured as
**silently failing** in the same test where a named-path instruction was followed correctly on
haiku/low. A shelf nothing names by name is reachable only by luck, which is why
`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/project/areas.md` calls a split finished by the
rebuild that renders it.

**The heading carries the absolute directory; the names beneath it are relative to that heading** -
the convention the register names already use, and an area line is one of those names. What rule 0
governs is a path written **outside** that heading's reach, which is exactly what an instruction
carries and why the render writes those in full.

**Which areas appear is the directory listing; what each line says comes from `NOTES.md`.** The
listing is authoritative for existence - an area created by a split appears at the next rebuild
without anyone editing a pointer. The cues that identify the topic are authored in `NOTES.md`'s
`## Read on demand` heading, which already requires a one-line description per on-demand file.

**Where `NOTES.md` has no line for an area, `/nk:project` derives the cue from that area's own
registers and writes it into `NOTES.md`** - not into the projection alone, or the next rebuild
derives it again. **This stays inside the scope**: an area of this project is this project's, so
reading it to describe it breaks nothing the *one scope* rule protects. **Say in the report that the
cue was derived**, because a described shelf and a described-by-us shelf are not the same claim.
Every other renderer - `save`, `doctor --fix`, `upgrade` - renders the line `NOTES.md` holds and
derives nothing: one command owns the rebuild, and a cue invented in passing by a checkpoint would
differ from the one beside it.

**An area with no line and no way to derive one renders as its slug alone**, and `/nk:doctor`
reports it: a slug is a guess about what the shelf holds, and the split that made it knew better.

**The catalogue is not the trigger.** Rendering an area by name makes it *findable* by a session
already wondering about that topic; it does not make it *load* when the topic is in play and nobody
wondered. That is what an entry in `instructions.md` does - *when the work touches X, read `<area>`
first* - and it is the deliberate upgrade for the areas that earn their always-loaded bytes, not a
line the render mints for every area on its own.

**Every projection carries a generation header** naming its source, the command that wrote it, and
the date - so anyone who finds one knows not to hand-edit it.

**Current work state is never projected.** `CLAUDE.local.md` wins on conflict and loads on every
prompt, so a stale *"you are on branch X"* is not a stale note somebody chose to read - it is an
authoritative false claim outranking the team's own file, and it goes stale the moment the branch
changes rather than at the pace knowledge decays. State lives in the active work item's `resume.md`;
the projection ends with the one line that gets you there.

**A projection is never the only copy of anything.** A fact that exists only here is a bug in the
promoting step, not a feature of the projection.

## Budgets, and what to drop

| Slice | Setting |
|---|---|
| repo | `projection_project_bytes` |
| workspace | `projection_workspace_bytes` |

**The values live in `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`**, with every other
default, so that `/nk:config` has one place to read and this file has one job. **Read them; do not
assume them** - a ceiling guessed wrong is a projection silently truncated or silently oversized.

**Over budget, degrade - never truncate.** A slice past its ceiling is delivered as a pointer and
its size, not as a silently shortened version.

**Each file carries one scope in two sections, and the ordering between them is fixed: the facts
degrade first, the instructions degrade last.** A fact delivered as a pointer is a read away from
being had; an instruction delivered as a pointer is one nobody is told to follow, and a dropped
instruction is **silently disobeyed** while the block still looks complete. So past the ceiling
`## Always needed here` degrades to a pointer whole, and `## Standing instructions` is the last
thing to go.

**If the instructions alone are past the slice's ceiling, say that in those words.** It is a
different condition from a large `NOTES.md` and it has a different remedy - retire an entry, or move
a conditional one into the area it belongs to - and reporting it as *over budget* sends the user to
trim the wrong file.

**The two files are budgeted separately and neither can evict the other.**

**Say what was dropped** - a projection that quietly lost half its content is worse than one that
was never written.

## Failing

**Store first, projections last.** A failed store write aborts the save. A failed projection is a
**warning**, never an abort, because a projection is derivable and `/nk:doctor --fix` rebuilds it.

**Name what was and was not written.** A projection step that reports files it did not write is the
defect class this step is most prone to.
