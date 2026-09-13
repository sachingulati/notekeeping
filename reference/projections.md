---
doc:   projections
title: Writing a projection, and never clobbering what you did not create
---

# Projections

**A projection is a copy of store content, generated into a file the harness loads by itself.** It
is never a source. Losing every projection loses nothing - `/nk:doctor --fix` rebuilds them all.

**Both targets are `CLAUDE.local.md`. Never write a file named `CLAUDE.md`, at any level.** That
file belongs to somebody at every level it appears - the team at a repo root, the user everywhere
else - and `CLAUDE.local.md` is the platform's own slot for uncommitted personal content. **The
plugin writes nothing named `CLAUDE.md` at any level, and since graduation was removed no command
proposes into one either.**

| Projection | Target | Source | Delivers |
|---|---|---|---|
| **repo** | `<repo>/CLAUDE.local.md` | `<store>/projects/<p>/NOTES.md` | project |
| **workspace** | `<workspace-root>/CLAUDE.local.md` | the workspace store's own `NOTES.md` | workspace |

**The repo projection carries the project and nothing else, always.** It does not walk on to the
workspace, because the workspace has a projection of its own and both files load in the same session
- so carrying it in both delivers every workspace fact twice, in the one place where bytes are
charged on every prompt. **Deliver each scope exactly once.**

## Three commands write a projection, and each owns a different moment

| Command | When | What it writes |
|---|---|---|
| **`/nk:init`** | **registration** - a project is added, or a workspace created | that project's projection, or the workspace one. **This is what creates a projection**; a registered project is delivering before any save has run |
| **`/nk:save`** | every checkpoint | the **active work item's project**, and the workspace projection. No other project, ever - it carries the project alone, so no other project's content can have changed |
| **`/nk:project`** | a rebuild | the named project's, or every project's in bare mode. **The repair path**, and the only thing that picks up a newly added area |

**`/nk:doctor` writes one only under `--fix`**, and reports otherwise.

**Existence is registration's job; freshness is the save's.** Keeping those apart is what closed
`OPEN.md` §12 (3.20): while the first save was also the first write, a repo could be registered,
mapped, reported by `doctor` as missing its file, and have no command the user could run to get one.

**There is no second shape, and that is deliberate.** This file used to have two legitimate
contents chosen by a condition evaluated elsewhere - whether the
workspace projection was being skipped - and the report had to say which one it had written. **That
branch caused two defects and prevented neither.** One content shape needs no condition, no
reporting rule, and nothing to re-evaluate when a registry changes.

*(Measured 2026-09-04: two runs of the same fixture, one carrying the workspace section and one not,
from a source column that said "the parent chain" without saying where it stopped. Measured again
2026-09-07: a workspace dropping to one project kept an ancestor file it should have folded in, and
the repo projection never switched - the branch decided once and never revisited.)*

`<workspace-root>` is **the directory holding that store's `.notekeeping/`** - the same walk that
resolved the store already produced it. It is never configured and never guessed.

**Global has no projection.** `~/.notekeeping/` is an ancestor of every workspace, so delivering it
would mean `~/CLAUDE.local.md`, charged in every session on the machine. Global is read on demand.

## Before writing anything

1. **Is the projection enabled?** `projections.enabled` for the repo one, `projections.workspace`
   for the workspace one. **Both default `true`** and both are store-scoped. **Disabled means write
   nothing and say nothing** - it is not a warning.
2. **Is there anything to render?** The workspace projection is written whenever its source has
   content - **the project count is irrelevant** (3.18). If `<store>/NOTES.md` is absent or empty
   there is simply nothing to render, so no file appears; that is an empty render, not a rule with
   a condition to re-evaluate.
3. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and the file
   definitions per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`.

## The no-clobber rule

**Check the target before every write.** There are three cases and no others:

| The target | Do |
|---|---|
| **does not exist** | Create it. We own all of it; the markers are just a generation boundary |
| **exists and has our markers** | Rewrite only what is between them. Everything outside is preserved verbatim |
| **exists without our markers** | **Append our block at the end.** Never modify a byte above it |

**Detection keys on the markers appearing anywhere in the file - never on the file starting with our
header.** Once a block has been appended, the header comment no longer sits at the top, so a
top-of-file test would fail to recognise our own work and append a second block on every save. Search
the whole file for `notes:begin`; that, and only that, decides between case 2 and case 3.

**Case 2 rewrites the block every time. Never compare the source to its last generation and
skip.** The block is rendered from the project directory as well as from `NOTES.md` - the
`Read on demand` line *is* the directory listing - so a source-file comparison misses a register
appearing or disappearing, and the projection under-delivers from then on. **Compare the rendered
block to what is between the markers**, which is both idempotent and correct: unchanged inputs
write nothing, and any input change lands.

*(Measured 2026-09-04: three registers were added to a project directory and the next save reported
"already match the current source content byte-for-byte" and wrote nothing. Deleting the target and
re-running produced a different file from the same store state - the `Read on demand` section the
regeneration had skipped.)*

**The third case appends rather than refusing, and the rule it protects is unchanged:
don't-clobber-a-file-we-did-not-create.** Some people hand-write a `CLAUDE.local.md`; some other tool
may create one. Neither is ours to *overwrite* - but refusing outright meant the notes were not
delivered at all in that repository, which is the one thing the projection exists to do. Appending
keeps both: **every byte above our block is untouched**, and the block converts the file into an
ordinary case-2 target from the next save onward.

**The notice goes in the header comment, not the body.** A projection is always-loaded content
charged on every prompt, so the body carries facts and nothing else; the header is where
*do not edit* already lives and is what a person editing the file actually reads:

```
<!-- GENERATED by the Notekeeping plugin from <source> - <date>
     Do not edit or delete this block; edits are overwritten, and to get it back after
     deleting it run /nk:project <name>. Everything above it is yours and is never
     modified. Remove it for good with: /nk:config set projections.enabled false -->
```

**A repo in this case still gets the ignore step.** The file now carries our content, so it is
ignored exactly as one we created - see below. *(Measured 2026-09-07: `save` refused the write and
skipped the ignore step while `doctor` reported the missing entry, leaving a warning no save could
ever clear.)*

## The ignore step, and its ordering

**Write `.git/info/exclude`. That is the whole ignore step - there is no `.gitignore` line and no
setting that adds one** (3.4). `.gitignore` is tracked and team-owned; the projection is not the
team's file. The exclude is local, untracked and immediate, which are the projected file's own
properties.

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
<!-- GENERATED by the Notekeeping plugin from /abs/path/to/ws/.notekeeping/projects/repo-a/NOTES.md
     <date> - do not edit; edits are overwritten -->
<!-- notes:begin -->

# repo-a - local (not committed)

## Always needed here
The facts you need on most tasks here

## Read on demand - /abs/path/to/ws/.notekeeping/projects/repo-a/
gotchas - patterns - decisions - domain - runbook - architecture - interfaces - areas/

Work in flight is not projected. Run `/nk:load` to restore it.

<!-- notes:end -->
```

**One source, one slice, and the header names exactly one file.** The repo projection renders from
that project's `NOTES.md` and from nothing else; the workspace projection renders from the store
root's. **A second `## From the ...` section is a defect, not a variant** - there is no scope left
for one to carry, which is what the Budgets section below means by *each carries one slice*.

*(This template rendered two slices until 2026-09-09, a section headed `## From the platform` and a
two-file header, left behind when a grouping scope above the project was deferred out of the
product. It survived three sweeps because the retired word and the template's word were different.)*

**Every path written into a projection is absolute.** Not store-relative, not repo-relative. The
projection lives in the repo and the store lives above it, so a path like
`.notekeeping/projects/repo-a/` resolves to nothing from where the file sits.

**This matters most for the `Read on demand` line, which is the whole on-demand half of delivery.**
It is an instruction that causes a read; a path that does not resolve produces the failure
`03-delivery.md` section 6 describes - the instruction is followed, the read is refused, and the
model improvises, which is worse than an error because the output looks like it had notes behind it.
The header's path is merely documentation, but it is written the same way for the same reason.

*(Measured 2026-09-04: the first run of this reference emitted `.notekeeping/projects/repo-a/` in
both places, from a template that said `<store>/` without saying what `<store>` expands to.)*

**Every projection carries a generation header** naming its source, the command that wrote it, and
the date - so anyone who finds one knows not to hand-edit it.

**Current work state is never projected.** `CLAUDE.local.md` wins on conflict and loads on every
prompt, so a stale *"you are on branch X"* is not a stale note somebody chose to read - it is an
authoritative false claim outranking the team's own file, and it goes stale the moment the branch
changes rather than at the pace knowledge decays. State lives in the active work item's `dev.md`;
the projection ends with the one line that gets you there.

**A projection is never the only copy of anything.** A fact that exists only here is a bug in the
promoting step, not a feature of the projection.

## Budgets, and what to drop

| Slice | Setting | Default |
|---|---|---|
| repo | `projection_project_bytes` | 8000 |
| workspace | `projection_workspace_bytes` | 4000 |

**Over budget, degrade - never truncate.** A slice past its ceiling is delivered as a pointer and
its size, not as a silently shortened version.

**Neither file has anything to drop against.** Each carries one slice, so past its ceiling it
degrades to a pointer whole. There is no ordering to apply within a file.

**The two files are budgeted separately and neither can evict the other** (3.18). There was once a
drop order across scopes, from the era when workspace content could be folded into the repo file and
the repo file carried two slices; with one scope per file there is no combined set to drop out of
(3.19).

**Say what was dropped** - a projection that quietly lost half its content is worse than one that
was never written.

## Failing

**Store first, projections last.** A failed store write aborts the save. A failed projection is a
**warning**, never an abort, because a projection is derivable and `/nk:doctor --fix` rebuilds it.

**Name what was and was not written.** A projection step that reports files it did not write is the
defect class this build has already produced once.
