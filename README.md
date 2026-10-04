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

Knowledge moves **one way: outward** — review can propose a move back, but nothing moves on its own.
A fact is promoted when it outlives the thing that taught it, and it lands at the **narrowest scope
that covers every source that taught it** — never by counting mentions.

## Install

```
/plugin marketplace add https://github.com/sachingulati/claude-plugins.git
/plugin install nk@sachingulati
```

Notekeeping is listed in the `sachingulati` marketplace, which fetches it from this repository. The
full URL clones over HTTPS; the `owner/repo` shorthand can clone over SSH, which fails on a machine
without a GitHub SSH key.

## Getting started

```bash
/nk:init --workspace              # the folder you are in becomes your workspace; or name one: --workspace ~/projects
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
  work/<bucket>/<id>/           requirements, plan, resume, session, test, test-manual, instructions, summary
```

`<bucket>` is the period of first work - `YYYY-MM` by default (`work_bucket: month`), e.g.
`work/2026-09/0007-scrollbar-fix/`.

## Commands

**Lifecycle**

| | |
|---|---|
| `/nk:work [what]` | start a work item, with requirements from a ticket, a file (`--requirements`) or the conversation. **Optional** — `/nk:save` mints one too. The argument is context, never an id: an item takes the next counter unless `--id` names one |
| `/nk:save` | checkpoint — rewrites the handover, promotes what outlived the task, drains memory |
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
| `/nk:adopt [path]` | read the notes you already have and **build the store out of them**. **Nothing is written without a yes** - the bare command may ask to narrow what it reads, then proposes, saves the proposal as a report, and asks once before it writes; `--apply` applies a saved report. Into the store it only ever copies; the one thing it removes is content it has already copied out of a context file, shown line by line in that same proposal |
| `/nk:doctor` | what is broken, drifting, or worth doing. `--fix` repairs only the unambiguous |
| `/nk:upgrade` | move a store to the schema version this plugin ships. **Nothing is written without a yes, or `--apply` on the report it saved** - the bare command reports the gap and the work it would do, and asks once. |
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

- **Notes are written by commands** - typed by you, or started by Claude when you ask.
- **`/nk:init` creates the first store**, in an empty directory; a directory that already holds
  notes is `/nk:adopt`'s. Installing the plugin writes nothing.
- **A read line is the one thing written without being asked each time.**
  `CLAUDE.local.md` gets one line in each repository you registered as a project and in your
  workspace root - **outside the store** - and global gets a rule file,
  `~/.claude/rules/notekeeping.md`. The line tells a session to read that scope's `NOTES.md`; the
  notes themselves stay in the store. **It is written when you register the project**, or create the
  workspace or global, so a repo starts delivering the moment it is added, and it does not change
  when the notes do - a save writes none. **Delivery follows registration**, and
  **`/nk:doctor --fix` writes back any that is missing**, so deleting one costs nothing.
- **A secret is stored only when you ask.** Ask Claude to keep a login or a token and `/nk:save`
  writes it to `secrets.md` at the scope it belongs to, with a `.gitignore` line beside it. It is never
  loaded into a session: the notes list it, and Claude reads it when a task needs a login rather than
  asking you again. Nothing else ever puts a credential in a store.
- **Everything outside the store is personal.** One `CLAUDE.local.md` per registered
  repository and one for the workspace root, the global rule file, one `.git/info/exclude` entry per
  repository, and the entries added to your own `~/.claude/settings.json` (*Requirements*, below) -
  all personal and none committed. **Read lines go only into `CLAUDE.local.md` and the global rule
  file.**
- **`/nk:adopt` is the one command that edits a `CLAUDE.md`, and only downward.** When it moves
  knowledge out of a context file into the store it offers to remove what moved — **only from a
  file in no work tree (your `~/.claude/CLAUDE.md`, usually) or a `CLAUDE.md`/`CLAUDE.local.md` the
  repository ignores, only once the content is in the store, and only on a yes to a proposal that
  showed every line.** Each file is copied whole into the store's `tmp/` first, so a trim can be
  reverted. Any other file in a repository is never touched. Facts go to the notes and instructions
  go to the scope's `instructions.md` - never merged, and never held back by a budget: one past it is
  reported, not left behind. Every entry is placed by what it is about: most of your
  `~/.claude/CLAUDE.md` lands in global, but a line about one project goes to that project.
- **Already have a `CLAUDE.local.md`? The line goes below it, and nothing above it is touched.**
  It is fenced by markers and labelled with what wrote it; only what is between those markers is
  ever rewritten. **Registering the project is the moment that append happens**, so you see it
  in that command's report. Delete the block and `/nk:doctor --fix` writes it
  back: it is maintained for as long as the project is registered.
- **Rename or move a repository inside its workspace and its notes keep working.** The line moves
  with the folder, and it is also how every command tells which project a folder is, so loading
  and saving carry on. The store's record of the old path goes stale: `/nk:save` and `/nk:doctor`
  say so in one line, and `/nk:init` from the folder updates it.
- **Tracked files stay as they are.** The line's file is ignored through one appended line in
  `.git/info/exclude`, which is local to your clone and never committed. Your `.gitignore` stays
  yours — that one lands in your team's review.
- **Registering a repository asks you once.** Claude Code treats anything under `.git` as a
  sensitive file, so the `.git/info/exclude` line is approved by you each time, whatever your
  settings allow. Decline it and the read line is still written - without it the repository gets
  none of its notes - but the file is then untracked and not ignored, so the report warns that a
  `git add -A` would commit it. `/nk:doctor --fix` retries the exclude, or add `/CLAUDE.local.md` to
  your `.gitignore`.

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
`/nk:adopt`, and no exclude line - add `/CLAUDE.local.md` to your `.gitignore` instead.

**Settings, written for you.** Your store sits above your repositories, and the read line in every
repository points at it by absolute path. Claude Code reads outside the working directory only where
`additionalDirectories` allows it, so **`/nk:init` adds to `~/.claude/settings.json`** - adding to
what is there, never replacing it:

- `permissions.additionalDirectories`: your workspace root, `~/.notekeeping`, and the plugin's own
  folder, so the notes and the plugin's rules are readable from any repository.
- `permissions.allow`: `Skill(nk:*)`, and `Edit(<store>/**)` for each store, so a command Claude
  starts, and its writes inside a store, run without a prompt. Writes outside a store still ask.

If that file cannot be parsed, `init` leaves it alone and prints the line to add by hand.
`/nk:doctor` reports a missing entry as an error, and its repair adds it.

## Licence

MIT — see [LICENSE](LICENSE).
