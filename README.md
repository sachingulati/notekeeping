# Notekeeping

**Notes for everything you work on - plain Markdown, in a directory you own.**

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

Knowledge moves **one way: outward** — review can propose a move back, but nothing moves on its own.
A fact is promoted when it outlives the thing that taught it, and it lands at the **narrowest scope
that covers every source that taught it** — never by counting mentions. A workspace can sit inside
another, and a wider scope — an enclosing workspace, or global — takes an entry only when it is worth
loading in every session that scope reaches.

## Install

```
/plugin marketplace add https://github.com/sachingulati/claude-plugins.git
/plugin install nk@sachingulati
```

Notekeeping is listed in the `sachingulati` marketplace, which fetches it from this repository. The
full URL clones over HTTPS, so it works on a machine with no GitHub SSH key.

## Getting started

Three steps, in this order:

1. **Install** the plugin.
2. **Register every workspace and project first** - `/nk:init` in each. Adopt places what it finds
   by what is registered, and an entry for a project it does not know stays where it was.
3. **Adopt, native first, as many runs as it takes.** The bare `/nk:adopt` reads your `CLAUDE.md`
   files and follows what they point at, up to a size limit per run (5 MB by default); run it again
   and it carries on where it stopped. A source already adopted is not read again. Several bounded
   runs cost less and place better than one large one.

```bash
/nk:init --workspace              # the folder you are in becomes your workspace; or name one: --workspace ~/projects
/nk:adopt                         # once every workspace and project is registered: your CLAUDE.md files and what they point at
/nk:adopt                         # again, until it says nothing is left
/nk:work                          # start a work item up front — optional; /nk:save mints one too
/nk:save                          # checkpoint: the record, promotion, memory drain
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
  NOTES.md, instructions.md …   the workspace's own knowledge, as it earns its place
  config.md                     schema_version, and your project registry
  index.md                      the work-item resolver — regenerated, never hand-written
  adopted.md, baseline.md       /nk:adopt's and /nk:doctor's own records
  tmp/                          saved reports, and adopt's copies of what it trimmed
  schema/                       your overlay — wins over everything shipped
  projects/<name>/              NOTES.md, overview.md, and registers as they earn their place
  work/<bucket>/<id>/           requirements, plan, resume, session, test, test-manual, instructions, summary
```

`<bucket>` is the period of first work - `YYYY-MM` by default (`work_bucket: month`), e.g.
`work/2026-09/0007-scrollbar-fix/`.

## Commands

**Lifecycle**

| | |
|---|---|
| `/nk:work [what]` | start a work item, with requirements from a ticket, a file (`--requirements`) or the conversation. **Optional** — `/nk:save` mints one too. The argument is context, never an id: an item takes the next counter unless `--id` names one |
| `/nk:save` | checkpoint — updates the task's record, promotes what outlived the task, drains memory |
| `/nk:load [query]` | resume. **Reads only** |

**Artifacts**, each written into the current work item

| | |
|---|---|
| `/nk:plan` | how the work will be done |
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
| `/nk:adopt [path]` | read the notes you already have - your `CLAUDE.md` files and what they point at, or a pile at `<path>` - and **build the store out of them**. It proposes, saves the proposal as a report, and **writes nothing without a yes**; `--apply` applies a saved report. A run stops at a size limit and the next carries on. The one thing it removes is a line it has copied into the store or dropped with a reason, shown line by line in the same proposal |
| `/nk:doctor` | what is broken, drifting, or worth doing. `--fix` repairs only the unambiguous |
| `/nk:upgrade` | move a store to the schema version this plugin ships. **Nothing is written without a yes, or `--apply` on the report it saved** - the bare command reports the gap and the work it would do, and asks once |
| `/nk:config` | show or change settings and a store file's budget, and say which file each value came from |
| `/nk:budget` | what the notes actually cost you — always-loaded, on-demand, registers. **Reads only** |
| `/nk:index` | rebuild the work-item resolver. Rarely typed; `save` and `work` keep it current |
| `/nk:help` | what each command does, what each file is for, what every term means. **Reads only** |

**Claude can run these too.** Ask in plain words - *save where we are*, *load the payments work* -
and Claude may start the command itself. It may also approve what it proposed: a flag such as
`--apply` or `--fix` is approval whoever passes it. To keep Claude from starting a command on its
own, deny it in your Claude Code settings - `"Skill(nk:adopt)"` and `"Skill(nk:adopt *)"` under
`permissions.deny` - and typing `/nk:adopt` yourself still works.

## Everything it writes, and where

- **Commands write the notes** - typed by you, or started by Claude when you ask. Installing the
  plugin writes nothing; `/nk:init` creates the first store, in an empty directory.
- **One read line per scope, written once, at registration.** Each registered repository and the
  workspace root get a `CLAUDE.local.md` holding one line that tells a session to read that scope's
  `NOTES.md`; global gets `~/.claude/rules/notekeeping.md`. The notes stay in the store, and the line
  does not change when they do, so a save writes none. `/nk:doctor --fix` writes back any line that
  goes missing.
- **Your own `CLAUDE.local.md` is kept.** The line is appended below it, between markers labelled with
  what wrote them, and only what is between the markers is ever rewritten.
- **Nothing is committed.** Each `CLAUDE.local.md` is ignored through one line in `.git/info/exclude`,
  local to your clone; your `.gitignore` is never touched. Claude Code asks before every write under
  `.git`. Decline it and the read line is still written, and the report warns that `git add -A` would
  commit it - `/nk:doctor --fix` retries, or add `/CLAUDE.local.md` to your `.gitignore`.
- **`~/.claude/settings.json` is added to, never replaced** - *Settings, written for you*, below.
- **Moving a repository inside its workspace keeps its notes working.** The read line moves with the
  folder and is how a command tells which project a folder is. `/nk:save` and `/nk:doctor` say when
  the registered path is stale, and `/nk:init` from the folder updates it.
- **`/nk:adopt` is the one command that edits a context file, and it only removes.** A line comes out
  once its content is in the store, or the proposal dropped it with a reason - from
  `~/.claude/CLAUDE.md`, or a `CLAUDE.md`/`CLAUDE.local.md` the repository ignores - on a yes to a
  proposal that showed every line. Each file is copied whole into the store's `tmp/` first, so a trim
  can be reverted. Facts go to the notes and instructions to `instructions.md`, and every entry lands
  at the scope it is about, never wider than its source was loaded.
- **A secret is stored only when you ask.** Ask Claude to keep a login or a token and `/nk:save`
  writes it to `secrets.md` at the scope it belongs to, with a `.gitignore` line beside it. It is
  never loaded into a session: the notes list it, and Claude reads it when a task needs the login.

## Make it yours

The overlay is the point, not an escape hatch. Drop a file into `<store>/schema/` and by default it
**replaces** the shipped definition wholesale — which files exist, what admits an entry, what each
is worth in bytes. Carrying `extends: shipped` instead makes it a **fragment**: only the fields or
sections it names are replaced, and everything else is inherited. A plugin update never touches
either form.

Turning a shipped file off is one line, `enabled: false` (two, with `extends: shipped`), and adding
a file of your own is one definition. Overlays cover definitions only; to change how a skill behaves,
fork the plugin and install your copy.

## Requirements

Claude Code, with Sonnet or Opus: notes reach a session through an instruction to read them, which
those models follow. **Git is optional.** Where it is installed, commands ask it for a repository's
root, branch, commit, remote, ignore state and recent commits; pointed at a folder other than the
one you are in, Claude Code asks you once before it runs there. Without it, a run says what it
skipped: no commit lists, a stamp written *unverified*, staleness not checked, no file trimmed by
`/nk:adopt`, and no exclude line - add `/CLAUDE.local.md` to your `.gitignore` instead. **A few
shell commands are used where available**: `wc -c` for exact file sizes, `git hash-object` to tell
whether a source changed since it was adopted, `cp` for adopt's byte-exact backup of a file before
it trims it, and `rm` for `/nk:doctor --fix` to remove an old saved report. Without them, sizes are
estimates, changes are not detected, the backup is copied through Claude, and an old report is
emptied instead. Nothing is pre-approved: your permission settings decide whether you are asked.

**Settings, written for you.** Your store sits above your repositories, and the read line in every
repository points at it by absolute path. Claude Code reads outside the working directory only where
`additionalDirectories` allows it, so **`/nk:init` adds to `~/.claude/settings.json`** - adding to
what is there, never replacing it:

- `permissions.additionalDirectories`: your workspace root, `~/.notekeeping`, and the plugin's own
  folder, so the notes and the plugin's rules are readable from any repository.
- `permissions.allow`: `Skill(nk:*)`, and `Edit(//<store>/**)` for each store, so a command Claude
  starts, and its writes inside a store, run without a prompt. Writes outside a store still ask.

If that file cannot be parsed, `init` leaves it alone and prints the line to add by hand.
`/nk:doctor` reports a missing entry as an error, and its repair adds it.

## Licence

MIT — see [LICENSE](LICENSE).
