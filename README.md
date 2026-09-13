# Notekeeping

**What this repository learned cannot reach the next one.**

The gotcha that cost you an afternoon, the decision nobody wrote down, the reason that config is the
way it is — it lives in one repo. The next project starts from nothing, and a clone takes none of it
with you.

Notekeeping moves knowledge across that boundary. Work items get a folder that accumulates; what
outlives the task is promoted into knowledge you keep.

**Markdown in a directory you own. No database, no hooks, no background capture.**

---

## The idea in one picture

```
you work      ──▶  work/<id>/                 bounded by one task
                        │  promotion — what outlives the task
                        ▼
                   projects/<name>/           bounded by one repo or initiative
                        │  promotion — what holds across the workspace
                        ▼
                   <workspace>/.notekeeping/  projects that sit together
                        │  admission — not about any codebase
                        ▼
                   ~/.notekeeping/            your working life
```

Knowledge moves **one way: outward.** Nothing flows back in. A fact is promoted when it outlives the
thing that taught it, and it lands at the **narrowest scope that covers every source that taught
it** — never by counting mentions.

## Getting started

```bash
/nk:init ~/projects      # names your workspace - creates it, plus global, and offers the repos in it
/nk:work                 # start something — it infers the title from the session
/nk:save                 # checkpoint: handover, promotion, memory drain
/clear
/nk:load                 # and you are back where you were
```

`/nk:save` exists so the session becomes **disposable**. Save, clear, load. Everything else follows
from that.

## What a store looks like

A store is a `.notekeeping/` directory at the root of its scope. There are two kinds and no others:
`~/.notekeeping/` is global, and `<workspace>/.notekeeping/` covers the projects in that workspace.
Resolution walks up from where you are; finding none is a refusal, never a guess.

```
<workspace>/.notekeeping/
  config.md                     schema_version, and your project registry
  schema/                       your overlay — wins over everything shipped
  projects/<name>/              NOTES.md, overview.md, and registers as they earn their place
  work/<YYYY-MM>/<id>/          requirements, plan, dev, test, summary
```

**Everything outside `.notekeeping/` is yours.** The plugin claims no ordinary word at any level —
that is why the directory is named the way it is.

## Commands

**Lifecycle**

| | |
|---|---|
| `/nk:work [what]` | start a work item. **Optional** — `/nk:save` mints one too. The argument is context, never an id: an item takes the next counter unless `--id` names one |
| `/nk:save` | checkpoint — rewrites the handover, promotes what outlived the task, drains memory |
| `/nk:load [query]` | resume. **Reads only** — it declares no write tool at all |

**Artifacts**, each written into the current work item

| | |
|---|---|
| `/nk:plan` | how the work will be done. Freezes once execution starts |
| `/nk:test` | how this is verified, by machine and by hand |
| `/nk:summary` | what happened, in plain language, for someone who was not involved |
| `/nk:how` · `/nk:api` | the terrain you had to understand; contracts while they move. **Off by default** |

**Knowledge**

| | |
|---|---|
| `/nk:project [name]` | report every project, or rebuild one project's digest and overview |
| `/nk:review` | read the store's content and propose what should change. Proposes and stops; `--apply` performs only the mechanical findings |

**Tooling**

| | |
|---|---|
| `/nk:init [path]` | create a store. Naming a workspace also creates global and offers the repositories under it. Makes directories only; never reads your files |
| `/nk:doctor` | what is broken, drifting, or worth doing. `--fix` repairs only the unambiguous |
| `/nk:config` | show or change settings, and say which file each value came from |
| `/nk:budget` | what the notes actually cost you — always-loaded, on-demand, registers |
| `/nk:index` | rebuild the work-item resolver. Rarely typed; `save` does it |
| `/nk:help` | what each command does, what each file is for, what every term means |

## Everything it writes, and where

- **No hooks, and no background capture.** Notes exist because you ran `/nk:save`. That is a real
  cost and it is the deliberate trade.
- **Nothing is created until you ask.** No store exists until `/nk:init` runs, and a non-empty
  directory is refused rather than adopted. Nothing is written on install or on first run.
- **One thing is written without being asked each time, and here it is. The projections.**
  `CLAUDE.local.md` is written to each repository you registered as a project, and to your workspace
  root - **outside the store**. **It is written when you register the project**, so a repo starts
  delivering the moment it is added; later saves rewrite only the one project you were working in, and
  `/nk:project <name>` refreshes any of them. That is how the notes reach a session at all:
  nothing else the plugin writes is loaded automatically. `projections.enabled: false` and
  `projections.workspace: false` stop them, and **`/nk:doctor --fix` rebuilds every projection**, so
  deleting one costs nothing.
- **Only `CLAUDE.local.md`, never `CLAUDE.md`.** That file is yours or your team's at every level it
  appears, and **nothing here writes it or proposes a change to it.** Every file this plugin writes
  outside its own store is personal and local — two `CLAUDE.local.md` files and one
  `.git/info/exclude` entry. **Nothing it writes is a file your teammates read.**
- **Already have a `CLAUDE.local.md`? We append below it and never touch what is above.** The block
  is fenced by markers and labelled with what wrote it; every later save rewrites only what is
  between those markers. **Registering the project is the moment that append happens**, so you see it
  in that command's report. Delete the block and `/nk:project <name>` or `/nk:doctor --fix` writes it
  back — to stop it for good, `/nk:config set projections.enabled false`.
- **No tracked file is touched.** The projection is ignored through `.git/info/exclude`, which is
  local to your clone and never committed. We do not write your `.gitignore` — that one lands in
  your team's review, so it stays yours.

## Make it yours

The overlay is the point, not an escape hatch. Drop a file into `<store>/schema/` and it **replaces**
the shipped definition wholesale — which files exist, what admits an entry, what each is worth in
bytes. A plugin update never touches it.

Turning something off is one line. Turning `/nk:how` on is the same line, inverted.

## Calling it from another tool

Commands accept `--caller <name>`, which declares the call came from a tool rather than a person.
Under it a command **never asks** — it refuses, naming the argument that would satisfy it — and ends
with a line you can parse:

```
nk: <command> <status> — <detail>
```

`ok`, `no-change` or `refused`. `no-change` is what makes retrying safe. `/nk:help --caller` reports
the contract version and the callable list.

## Requirements

Claude Code. Git is used where it can answer and is never required — a project with no repository is
a first-class case.

## Licence

MIT — see [LICENSE](LICENSE).
