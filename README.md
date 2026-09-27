# Notekeeping

**Notes for everything you work on.**

**Everything you work on is saved, so you can pick it up later.** Each task gets its own record:
what was asked, the plan, what happened, and how it was checked. Close the session, come back
tomorrow or next month, and `/nk:load` puts you back where you left off.

**Everything you learn adds up to knowledge.** The gotcha that cost you an afternoon, the decision
nobody wrote down, the reason that config is the way it is. When something is still true after the
task ends, it moves outward to where it applies: the project, the workspace, or everywhere you
work. Each task leaves the next one better informed.

**Both happen in one step, while you work.** `/nk:save` updates the task's record and moves what it
taught you outward at the same time. You never have to sit down and write things up.

**So your session is disposable, and what you learned in it is not.** Long sessions get worse: dead
ends pile up, each turn costs more, and the agent stays confident about things that stopped being
true an hour ago. When the record is current you can `/clear` and lose nothing.

**Markdown in a directory you own.**

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
| `/nk:summary` | what happened, in plain language, for someone who was not involved - and offers it as a page others can read and comment on |

**Knowledge**

| | |
|---|---|
| `/nk:project [name]` | report every project, or rebuild one project's digest and overview |
| `/nk:review` | read the store's content and propose what should change. Proposes, saves the proposal as a report, and asks; `--apply` applies a saved report - all of it, the findings you name, or one at a time |

**Tooling**

| | |
|---|---|
| `/nk:init [path]` | create a store. Naming a workspace also creates global and offers the repositories under it. Creates structure; never mines your notes |
| `/nk:adopt [path]` | read the notes you already have and **build the store out of them**. **Nothing is written without a yes** - the bare command proposes and asks once. Into the store it only ever copies; the one thing it removes is content it has already copied out of a context file, shown line by line in that same proposal |
| `/nk:doctor` | what is broken, drifting, or worth doing. `--fix` repairs only the unambiguous |
| `/nk:upgrade` | move a store to the schema version this plugin ships. **Nothing is written without a yes, or `--apply` on the report it saved** - the bare command reports the gap and the work it would do, and asks once. |
| `/nk:config` | show or change settings, and say which file each value came from |
| `/nk:budget` | what the notes actually cost you — always-loaded, on-demand, registers |
| `/nk:index` | rebuild the work-item resolver. Rarely typed; `save` does it |
| `/nk:run [name]` | run a command you wrote in your store's `schema/commands/`, under the store's rules - it writes only what its `writes:` declares, and only inside the store. Run bare, it lists the commands you have defined there |
| `/nk:help` | what each command does, what each file is for, what every term means |

## Everything it writes, and where

- **Nothing runs in the background.** The plugin installs no hooks: notes exist because you ran
  `/nk:save`. Want capture anyway? A hook of your own can run `/nk:save` headlessly with `--oneline` -
  `reference/consumer-contract.md` says how.
- **Nothing is created until you ask.** No store exists until `/nk:init` runs, and a non-empty
  directory is refused rather than adopted. Nothing is written on install or on first run.
- **One thing is written without being asked each time, and here it is. The projections.**
  `CLAUDE.local.md` is written to each repository you registered as a project, to your workspace
  root, and to your home directory for global - **outside the store**. **It is written when you
  register the project**, or create the workspace or global, so a repo starts delivering the moment
  it is added; later saves rewrite only the one project you were working in, plus the workspace and
  global, and `/nk:project <name>` refreshes any project's. The home-directory one loads in every
  session under your home directory, whether or not it touches a store. That is how the notes reach a session at all -
  nothing else the plugin writes is loaded automatically. **Delivery follows registration**, and
  **`/nk:doctor --fix` rebuilds every projection**, so deleting one costs nothing.
- **Nothing outside the store is a file your teammates read.** Three `CLAUDE.local.md` files, one
  `.git/info/exclude` entry and your store roots added to your own `~/.claude/settings.json`, all
  personal and none committed. **No projection is ever written to a
  `CLAUDE.md`.**
- **`/nk:adopt` is the one command that edits a `CLAUDE.md`, and only downward.** When it moves
  knowledge out of a context file into the store it offers to remove what moved — **only lines that
  are not committed, only once the content is in the store, and only on a yes to a proposal that
  showed every line.**
  Anything at `HEAD` is untouchable. Facts go to the notes and instructions go to the scope's
  `instructions.md` - never merged - and an instruction that does not fit its budget stays where it
  was. Your own `~/.claude/CLAUDE.md` is read the same way: its knowledge and its instructions move
  into global.
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

Turning a shipped file off is one line, `enabled: false`, and adding a file of your own is one
definition. A command of your own is one file too, `<store>/schema/commands/<name>.md`,
run as `/nk:run <name>`.

## Calling it from another tool

Every command accepts `--oneline`. **It changes the output and nothing else**: the command does what
it does for you, and prints one line you can parse instead of its report:

```
nk: <command> <status> — <detail>
```

`ok`, `no-change` or `refused`. Where the command would have asked you something it refuses instead,
and the line carries the question and its options. A flag is approval whoever types it, so `--apply`
and `--fix` work as they do for you. A report too long for one line - `review`, `doctor`, `adopt`,
`upgrade` - is saved to a file the line names, and `--apply` applies it. `no-change` is what makes retrying safe. `/nk:help --oneline`
reports the contract version and the command list.

## Requirements

Claude Code. Git is used where it can answer and is never required.

**One setting, written for you.** Your store sits above your repositories, and the `Read on demand`
line in every projection points at it by absolute path. Claude Code reads outside the working
directory only where `additionalDirectories` allows it, so **`/nk:init` adds your workspace root and
`~/.notekeeping` to `~/.claude/settings.json`** - adding to what is there, never replacing it:

```json
{ "permissions": { "additionalDirectories": ["/path/to/your/workspace", "/home/you/.notekeeping"] } }
```

If that file cannot be parsed, `init` leaves it alone and prints the line to add by hand.
`/nk:doctor` reports a missing entry as an error, and its repair adds it.

## Licence

MIT — see [LICENSE](LICENSE).
