---
description: Report every project, or rebuild one project's two documents.
argument-hint: "[name] [--dry-run] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git rev-parse:*), Bash(git log:*), Bash(git remote:*), Bash(git -C:*)
---

Report the store's projects, or rebuild one project's documents. **It owns exactly two files** -
`NOTES.md` and `overview.md` - and writes no other file in any project.

**`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

Resolve the store first, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and resolve
`<name>` against the projects registered in it. **Never invent a project.** Resolve both file
definitions through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` before writing either.

**The rebuild is a migration-on-write carrier, and it is the only carrier `overview.md` has.** Where
a resolved definition's `## Migration` declares a step **on write**, convert the file and **stamp it
in the same write** - `<!-- nk: schema N -->`, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` - and name the conversion in the report. Where
it declares **upgrade only**, do not convert and do not write that file: report it outstanding and
name `/nk:upgrade`. **A conversion without its stamp is the worst of both** - the file is at the new
shape, every run still reads it as outstanding, and the next rebuild converts it again for ever.
Never walk the store for files you were not already writing, and **never move `schema_version`**:
this command finishes the files in its hands and nothing else.

| Mode | Does | Writes |
|---|---|---|
| bare | Report every project | no |
| `<name>` | Rebuild that project's two documents and stamp them | yes |
| `<name> --dry-run` | What would change, and which facts look stale | no |

## What it owns, and what it must never touch

**Two documents, and being owned is what makes them safe to be documents at all.**

| | |
|---|---|
| `NOTES.md` | the always-loaded digest. `/nk:save` maintains its **Active** pointer and its stamp at each checkpoint; **this command rebuilds the file** |
| `overview.md` | orientation in fields, assembled from sources. Rebuilt on request, and never appended to by promotion |

**A rebuild also refreshes the repo projection**, per
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`. The
projection is derived from `NOTES.md` and the directory listing, so rebuilding the source and leaving
the delivered copy stale would leave the user reading yesterday's file. **This is also the only thing
that writes a new area's cue into `NOTES.md`**: `## Read on demand` renders from the listing, so an area added by a
promotion or by `/nk:review`'s split is not reachable from a session until something re-renders it -
and this is that something.

**Registers and ledgers are never regenerated** - `gotchas` - `patterns` - `decisions` - `domain` -
`runbook` - `architecture` - `areas/` - **and `instructions`, which is not a register
but is written the same way**. They are appended to by promotion and edited in place by
re-promotion. **A rebuild that touched one would destroy accumulated knowledge to refresh a stamp**,
so read them freely and write none of them.

**What a rebuild does pick up from them is the rendering** - the areas that now exist, and the
standing instructions that now sit beside `NOTES.md`. That is the whole of why this command is what
finishes a split.

## Bare - the report

Every project in the store.

| Reported | What counts |
|---|---|
| **Inventory** | which of the scope's files exist, which are missing, and any registered project with no directory |
| **Staleness, on both axes** | `repo <sha> (<date>)` and `env <name> <build> (<date>)`. **Either threshold trips it** - `staleness_warn_commits` or `staleness_warn_days`, resolved per `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md` and never assumed. The env axis is mandatory on `runbook.md` and optional elsewhere |
| **Authority violations** | a file **declaring** `authority: derived` that carries no refresh recipe |
| **`NOTES.md` against budget** | its size against the resolved definition's `budget`, per project |

**The shape, filled from the run and never copied from here:**

```
<project>
  inventory     <files present>; missing <files> ; registered with no directory <names>
  staleness     repo <sha> (<date>) | <not a repository> | <could not determine> | unknown
                env <name> <build> (<date>) | unstamped
                tripped: <axis> at <number> against <threshold>
  authority     <file> declares derived and carries no recipe
  NOTES.md      <used> / <budget>
```

**One block per project, in the order the store registers them**, and every line present for every
project - a project with nothing to report carries the empty render, not a gap.

**The authority check runs one way only.** Never flag a file for being un-derived. That inverted
check fires on every `overview.md` and `architecture.md` in a store on day one, with no recipe
available for any of them, and a report nobody can get to green teaches that the report is optional.

**A commit count alone is not staleness.** Fifty commits is a week in one repo and a year in another,
means nothing across a rebase, and means nothing at all at workspace or global scope, neither of
which has a repo. Report the axis that tripped and the number behind it, not a verdict.

**Where git cannot answer, say so rather than reporting freshness.** The three outcomes in
`store-boundary.md` apply here in full: *not a repository* and *could not determine* are different
claims, and the repo axis is **unknown** for the third, never fresh.

## `<name>` - the rebuild

**Rebuilding `NOTES.md` is three derived sections and one preserved one.** Get this wrong and a
rebuild silently deletes the digest it exists to maintain.

| Section | On rebuild |
|---|---|
| `## Active` | **derived** - re-read from the store's open work items for this project. One line each, a pointer, never a copy of the state |
| `## Always needed` | **preserved.** This is accumulated knowledge that arrived by promotion and exists nowhere else. Carry it across unchanged |
| `## Read on demand` | **derived** - rendered from the directory listing, so a register added since the last rebuild appears without being told |
| the `verified` stamp | **derived** - see below |

`overview.md` is rebuilt whole, because every field in it is assembled: stack, language and build
versions, packaging, role, repos spanned, upstream and downstream, entry points, owner, links, one
diagram. `git remote` is the source for the repository's links, as the build file and `README.md` are for
the rest. **Record what it was assembled from** in the header's `sources:` list - that list is what
makes the authority claim honest - and set `authority: derived` only when every field came from a
recorded source, `original` otherwise.

A repository's own committed instructions file is a **legitimate source** for these fields. It is
never a deletion criterion for anything, and nothing ever flows from it into a register.

### The stamp

```markdown
<!-- verified: repo <sha> (<date>) - env <name> <build> (<date>) -->
```

The sha comes from `git rev-parse HEAD`, and from nowhere else. **Never invent the env half:** it
cannot be derived from a repository, so carry forward what the file already had, or use what the
user supplied, or omit the axis and say it is unstamped. An invented environment is a false claim
that outranks the truth for as long as nobody checks it.

### After writing

`NOTES.md` is a projection source. Regenerate the affected
projections per `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - **store first, projections last**
- and say which files were rewritten. A registered project is delivered; a source with no content
renders nothing, which is an empty render rather than a skip.

### `--dry-run`

Print the diff for both files and which facts look stale. Write nothing, including projections.

## The outcome line

Emit it only under `--oneline`, as the last line:

```
nk: project ok — repo-a, 2 files
nk: project no-change — repo-a
```

**No-op when nothing changed.** A rebuild whose output is byte-identical to what is on disk writes
nothing and reports `no-change`. Compare the rendered file to the file, never the sources to their
last generation.

## Never

Write any file in the project other than `NOTES.md` and `overview.md`. Write outside the store at
all, except the projections this command regenerates - **and nothing this command writes is a file
anyone else reads.** Commit anything. Regenerate a register. Invent a project, a setting name, an environment, or a repo root. Report on anything
outside the store **beyond the registered repository's own git state**, which is what the staleness
axis is - everything else outside `.notekeeping/` belongs to the user, and will outlive this
plugin.
