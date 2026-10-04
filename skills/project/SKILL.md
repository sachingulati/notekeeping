---
name: project
description: Report every project registered in the notes, or rebuild one project's NOTES.md and overview.md. Use when the user asks for the state of the registered projects, or to rebuild a project's notes overview.
argument-hint: "[name] [--dry-run] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

Report the store's projects, or rebuild one project's documents. It owns exactly two files -
`NOTES.md` and `overview.md` - and writes no other file in any project.

Before anything else, in order:
1. **`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
2. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`.
3. **An overlay?** If `<store>/schema/skills/project/` exists, follow `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`: a `SKILL.md` there replaces the rest of this file, and a file under its `references/` replaces the shipped reference of that name wherever this skill cites it.
4. **Infer before asking**: read what the conversation already states, and ask only what is still open.

Resolve
`<name>` against the projects registered in the store. **Never invent a project.** Resolve both file
definitions through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` before writing either.
A write that leaves either file at or past `budget_notice_pct` of its `budget:` says so, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`.

The rebuild is a migration-on-write carrier, and it is the only carrier `overview.md` has. Where
a resolved definition's `## Migration` declares a step on write, convert the file and stamp it
in the same write - `<!-- nk: schema N -->`, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`) - and name the conversion in the report. Where
it declares upgrade only, do not convert and do not write that file: report it outstanding and
name `/nk:upgrade`. A conversion without its stamp is the worst of both - the file is at the new
shape, every run still reads it as outstanding, and the next rebuild converts it again for ever.
Never walk the store for files you were not already writing, and **never move `schema_version`**:
this command finishes the files in its hands and nothing else.

| Mode | Does | Writes |
|---|---|---|
| bare | Report every project | no |
| `<name>` | Rebuild that project's two documents and stamp them | yes |
| `<name> --dry-run` | What would change, and which facts look stale | no |

## What it owns, and what it must never touch

Two documents, and being owned is what makes them safe to be documents at all.

| | |
|---|---|
| `NOTES.md` | the always-loaded digest. `/nk:save` maintains its Active pointer and its stamp at each checkpoint; this command rebuilds the file |
| `overview.md` | orientation in fields, assembled from sources. Rebuilt on request, and never appended to by promotion |

**A rebuild writes no read line in the repository.** The line in its `CLAUDE.local.md` points at
`NOTES.md` and does not change when it does; `NOTES.md`'s own read lines are rendered with it. Areas are not listed here: `/nk:review`'s split and promotion
append a row to `areas/INDEX.md`, and `NOTES.md`'s read line to that file is what reaches them.

**Registers and ledgers are never regenerated** - `gotchas` - `patterns` - `decisions` - `domain` -
`runbook` - `architecture` - `areas/` - and `instructions`, which is not a register
but is written the same way. They are appended to by promotion and edited in place by
re-promotion. A rebuild that touched one would destroy accumulated knowledge to refresh a stamp,
so read them freely and write none of them.

What a rebuild does pick up from them is the rendering - the areas that now exist, and the
standing instructions that now sit beside `NOTES.md`. That is the whole of why this command is what
finishes a split.

## Bare - the report

Every project in the store.

| Reported | What counts |
|---|---|
| **Inventory** | which of the scope's files exist, which are missing, and any registered project with no directory |
| **Staleness, on both axes** | per `${CLAUDE_PLUGIN_ROOT}/reference/staleness.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md` for the sha, and `staleness_warn_days` per `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`) - read off each file's `verified:` stamp. Only days trip; *HEAD moved* is shown, never tripped |
| **Authority violations** | a file declaring `authority: derived` that carries no refresh recipe |
| **`NOTES.md` against budget** | its size against the resolved definition's `budget`, per project |

The shape, filled from the run and never copied from here:

```
<project>
  inventory     <files present>; missing <files> ; registered with no directory <names>
  staleness     <the four lines staleness.md gives>
  authority     <file> declares derived and carries no recipe
  NOTES.md      <used> / <budget>
```

One block per project, in the order the store registers them, and every line present for every
project - a project with nothing to report carries the empty render, not a gap.

The authority check runs one way only. Never flag a file for being un-derived. That inverted
check fires on every `overview.md` and `architecture.md` in a store on day one, with no recipe
available for any of them, and a report nobody can get to green teaches that the report is optional.

## `<name>` - the rebuild

Rebuild both files per `${CLAUDE_PLUGIN_ROOT}/reference/overview.md` (with
`${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md` for the sha and the git directory, and
`${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` for `## Active`) - which sections are derived and
which preserved, the repository link, the stamp, and the no-op. The stamp's date is today's, the
day it is written.

After writing, say which files were rewritten - or `no-change` - and name every field carried
across verbatim and a link left out. Nothing outside the store is written.

**`--dry-run`** prints the diff for both files and the staleness lines, and writes nothing.

## Never

Write any file in the project other than `NOTES.md` and `overview.md`. Write outside the store at
all - **and nothing this command writes is a file
anyone else reads.** Commit anything. Regenerate a register. Invent a project, a setting name, an environment, or a repo root. Report on anything
outside the store **beyond the files under the project's repository's `.git` that the stamp and the
link read** - everything else outside `.notekeeping/` belongs to the user. Run git, or any command:
this skill reads files.
