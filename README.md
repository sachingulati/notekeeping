# <img src="assets/nk.png" alt="Nk" height="40" align="center"> Notekeeping

**Notes for everything you work on, growing into knowledge as you go.**

## The problem

When you work with an AI agent, what you learn lives in the session, and sessions don't last.

- **Long sessions get worse.** Dead ends pile up, each turn costs more, and the agent stays sure of
  things that stopped being true an hour ago.
- **Compacting doesn't fix it, and it happens without you.** When the context fills, or when you step
  away from a long session for about an hour, Claude Code summarises it to make room, and you don't
  choose what the summary keeps. The exact path, the reason behind a choice, the approach already
  tried and ruled out, a rule you gave an hour ago: these are what can drop out.
- **Clearing loses your place.** What was asked, the plan, what was tried. Tomorrow you rebuild it
  from scratch.
- **What a task taught you dies with it.** The trap that cost an afternoon, a decision and its
  reason. The next task, and the next project, learn it again.
- **Writing it up is a separate chore,** so it rarely happens.

## What Notekeeping does

**Everything you work on is saved, so you can pick it up later.** Each task gets its own record:
what was asked, the plan, what happened, and how it was checked. Close the session, come back
tomorrow or next month, and `/nk:load` puts you back where you left off.

**Everything you learn adds up to knowledge.** When something is still true after the task ends, it
moves outward to where it applies: the project, the workspace, or everywhere you work. Each task
leaves the next one better informed.

**Both happen in one step, while you work.** `/nk:save` updates the task's record and moves what it
taught you outward at the same time. You never have to sit down and write things up.

**So your session is disposable, and what you learned in it is not.** Save, then `/clear`, and you
decide what is kept - not a summary.

**It is all plain Markdown, in a directory you own.**

## The idea in one picture

```
you work      ──▶  work/<id>/                 one task
                        │  what outlives the task
                        ▼
                   projects/<name>/           one repository
                        │  what holds across its projects
                        ▼
                   <workspace>/.notekeeping/  the projects that sit together
                        │  what is about you, not any codebase
                        ▼
                   ~/.notekeeping/            everywhere you work
```

Knowledge moves **one way: outward**, to the narrowest place it holds.

## Install

```
/plugin marketplace add https://github.com/sachingulati/claude-plugins.git
/plugin install nk@sachingulati
```

## Getting started

1. **Install** the plugin.
2. **`/nk:init --workspace`** in the folder that holds your repositories. It sets up the workspace,
   and offers to register the repositories under it.
3. **`/nk:adopt`** from the workspace, then from each project that has its own `CLAUDE.md`, to bring in
   what your `CLAUDE.md` files and notes already hold. Each run reads the `CLAUDE.md` files its folder
   loads - a project's own is read only from inside that project. It reads a bounded amount per run,
   so run it again until it says nothing is left, and nothing is written without your yes. It fills
   this workspace's notes and your global ones; for another workspace, run it from there.

Then the loop:

```bash
/nk:work                          # start a work item - optional; /nk:save makes one too
/nk:save                          # checkpoint: the task's record, and what it taught you
/clear
/nk:load                          # and you are back where you were
```

**Save before you step away, or turn off idle compaction.** Claude Code compacts a long session you
have left for about an hour, and a save after that records only what the summary kept. `/nk:init`
offers to turn it off; you can also do it in `/config` (*Idle compaction*), or with
`"idleCompaction": false` in `~/.claude/settings.json`. The cost: your first message after a long
break re-reads the whole conversation uncached.

## What a store looks like

A store is a `.notekeeping/` directory: `~/.notekeeping/` is global, and `<workspace>/.notekeeping/`
covers the projects in that workspace.

```
<workspace>/.notekeeping/
  NOTES.md, instructions.md …   the workspace's own knowledge
  config.md                     settings, and the registered projects
  index.md                      the list of work items - rebuilt, never hand-written
  schema/                       your own file definitions, if any
  projects/<name>/              each project's notes
  work/<YYYY-MM>/<id>/          each work item: requirements, plan, session, test, summary …
```

A run of `/nk:adopt`, `/nk:doctor`, `/nk:review` or `/nk:upgrade` that finds something is kept as a
work item too, so a stopped run resumes and a finding you declined stays declined.

## Commands

**Lifecycle**

| | |
|---|---|
| `/nk:work [what]` | start a work item. Optional - `/nk:save` makes one too |
| `/nk:save` | checkpoint - the task's record, what it taught you, and Claude Code's memory for this folder |
| `/nk:load [query]` | resume where you left off. **Reads only** |

**Write-ups**, each kept in the current work item

| | |
|---|---|
| `/nk:plan` | how the work will be done |
| `/nk:test` | how it is verified, by machine and by hand |
| `/nk:summary` | what happened, in plain language, for someone who was not involved |

**Knowledge**

| | |
|---|---|
| `/nk:project [name]` | the state of every project, or rebuild one project's `NOTES.md` and overview |
| `/nk:review` | propose what to merge, split or retire in the notes. Asks before changing anything |

**Setup and upkeep**

| | |
|---|---|
| `/nk:init [path]` | set up a workspace or register a project |
| `/nk:adopt [path]` | build the notes from what you already have. Asks before writing anything |
| `/nk:doctor` | what is broken or worth doing; `--fix` repairs the unambiguous |
| `/nk:upgrade` | bring a store up to the format this plugin version uses |
| `/nk:config` | show or change settings |
| `/nk:budget` | what the notes cost in context. **Reads only** |
| `/nk:index` | rebuild the work-item list. Rarely needed |
| `/nk:help` | what each command and file is for. **Reads only** |

**Claude can run these too.** Ask in plain words - *save where we are*, *load the payments work* -
and Claude may start the command itself, including a flag such as `--apply` or `--fix`. To stop
that for a command, add it to `permissions.deny` in your Claude Code settings (`"Skill(nk:adopt)"`);
typing it yourself still works.

## What it writes outside the notes

- **One read line per scope.** Each registered repository and the workspace root get a
  `CLAUDE.local.md` line telling a session to read the notes; global gets
  `~/.claude/rules/notekeeping.md`. Your own `CLAUDE.local.md` content is kept.
- **Nothing is committed.** Each `CLAUDE.local.md` is ignored through `.git/info/exclude`, local to
  your clone; your `.gitignore` is never touched.
- **`~/.claude/settings.json` is added to, never replaced** - read access to the notes and the
  plugin, permission for the commands to run and write inside the notes, and `idleCompaction: false`
  if you said yes to it.
- **`/nk:adopt` removes lines from your `CLAUDE.md` files** - only lines it moved into the notes or
  dropped with a reason, never from a committed file, on your yes, with each file backed up first.
- **Claude Code's memory for the folder you are in** is moved into the notes by `/nk:save`.
- **A secret is stored only when you ask**, in a `secrets.md` that is never loaded into a session.
- **One session writes a store at a time.** Two sessions saving into one store at once can lose a
  change.

## Requirements

Claude Code, with Sonnet or Opus on a 1M context window. **Haiku is refused.** **Git is optional**:
without it, a run says what it skipped.

## Licence

MIT — see [LICENSE](LICENSE).
