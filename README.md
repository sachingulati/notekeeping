# Notekeeping

**Your session is disposable. What you learned in it is not.**

**Every task leaves something behind.** The gotcha that cost you an afternoon, the decision nobody
wrote down, the reason that config is the way it is. Each work item gets a folder that accumulates —
what was asked, the plan, what happened, how it was verified — and what outlives the task is
promoted outward: into the project, into the workspace, into knowledge you carry wherever you work.

**Because the notes exist, the session stops being precious.** A long session is a worse session:
context fills with dead ends and superseded plans, it costs more per turn, and the agent grows
confident about things that stopped being true an hour ago. When the record is current you can
`/clear` and lose nothing — `/nk:load` rebuilds your context *out of the notes*, short and true.

**One step writes for both horizons, and that is the whole design.** `/nk:save` brings the record up
to date and promotes what outlived the task at the same time: the handover that restores you
tomorrow and the gotcha that saves you next year come out of one act of writing.

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
/nk:init --workspace ~/projects   # names your workspace - creates it, plus global, and offers the repos in it
/nk:work                          # start a work item up front — optional; /nk:save mints one too
/nk:save                          # checkpoint: handover, promotion, memory drain
/clear
/nk:load                          # and you are back where you were
```

**That loop is the habit; the store is what it leaves behind.** Each save makes the session safe to
discard and promotes what it taught you — so the knowledge base is built by working, and never by
remembering to write it up.

## What a store looks like

A store is a `.notekeeping/` directory at the root of its scope. There are two kinds and no others:
`~/.notekeeping/` is global, and `<workspace>/.notekeeping/` covers the projects in that workspace.
Resolution walks up from where you are; finding none is a refusal, never a guess. Global also keeps
a list of the workspaces you have created — the one thing no walk can tell you — and it is used to
enumerate them, never to resolve one.

```
<workspace>/.notekeeping/
  config.md                     schema_version, and your project registry
  index.md                      the work-item resolver — regenerated, never hand-written
  schema/                       your overlay — wins over everything shipped
  projects/<name>/              NOTES.md, overview.md, and registers as they earn their place
  work/<YYYY-MM>/<id>/          requirements, plan, resume, session, test, summary
```

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
| `/nk:review` | read the store's content and propose what should change. Proposes and stops; `--apply` shows each finding's diff and you pick which to apply |

**Tooling**

| | |
|---|---|
| `/nk:init [path]` | create a store. Naming a workspace also creates global and offers the repositories under it. Creates structure; never mines your notes |
| `/nk:adopt [path]` | read the notes you already have and **build the store out of them**. **Nothing is written without `--apply`** - the bare command proposes and stops. Into the store it only ever copies; the one thing it removes is content it has already copied out of a context file, on a second confirmation of its own |
| `/nk:doctor` | what is broken, drifting, or worth doing. `--fix` repairs only the unambiguous |
| `/nk:upgrade` | move a store to the schema version this plugin ships. **Nothing is written without `--apply`, and `--apply` still asks once** - the bare command reports the gap and the work it would do. **A store made before schema 2 has to run this before `/nk:save` will checkpoint it** |
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
  delivering the moment it is added; later saves rewrite only the one project you were working in,
  and `/nk:project <name>` refreshes any of them. That is how the notes reach a session at all -
  nothing else the plugin writes is loaded automatically. **Delivery follows registration**, and
  **`/nk:doctor --fix` rebuilds every projection**, so deleting one costs nothing.
- **Nothing outside the store is a file your teammates read.** Two `CLAUDE.local.md` files and one
  `.git/info/exclude` entry, all personal and none committed. **No projection is ever written to a
  `CLAUDE.md`.**
- **`/nk:adopt` is the one command that edits a `CLAUDE.md`, and only downward.** When it moves
  knowledge out of a context file into the store it offers to remove what moved — **only lines that
  are not committed, only once the content is in the store, and only on a confirmation of its own.**
  Anything at `HEAD` is untouchable, and an instruction you obey is never treated as knowledge.
- **Already have a `CLAUDE.local.md`? The block goes below it, and nothing above it is touched.**
  It is fenced by markers and labelled with what wrote it; every later save rewrites only what is
  between those markers. **Registering the project is the moment that append happens**, so you see it
  in that command's report. Delete the block and `/nk:project <name>` or `/nk:doctor --fix` writes it
  back: it is maintained for as long as the project is registered.
- **No tracked file is touched.** The projection is ignored through one appended line in
  `.git/info/exclude`, which is local to your clone and never committed. Your `.gitignore` stays
  yours — that one lands in your team's review.

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

**One setting, once.** Your store sits above your repositories, and the `Read on demand` line in
every projection points at it by absolute path. Claude Code reads outside the working directory only
where `additionalDirectories` allows it, so name your workspace root and `~/.notekeeping` in
`~/.claude/settings.json`:

```json
{ "permissions": { "additionalDirectories": ["/path/to/your/workspace", "~/.notekeeping"] } }
```

Without it the always-loaded half still arrives and the on-demand half is refused at the moment it
is read. `/nk:doctor` reports that as an error until it is set.

## Licence

MIT — see [LICENSE](LICENSE).
