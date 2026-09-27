---
description: What each command does, what each file is for, and what every term means.
argument-hint: "[topic] [--oneline]"
allowed-tools: Read, Glob
---

Explain Notekeeping. `$ARGUMENTS` narrows to one topic - a command, a file, or a term.

## With no argument

Three short sections, in this order:

**The loop.** `/nk:work` to start, `/nk:save` to checkpoint, `/clear`, `/nk:load` to come back.
That is the whole product; everything else supports it. **`/nk:work` is optional** - a save with
nowhere to write mints the bundle itself, so you can just work and save.

**The commands**, grouped by family, one line each:
- *lifecycle* - `load` `save` `work`
- *artifact* - named exactly after the file each produces: `plan` `test` `summary`
- *knowledge* - `project` `review`
- *tooling* - `config` `doctor` `help` `index` `init` `budget` `adopt` `upgrade` `run` - and `run`
  is how the user's own commands, in `<store>/schema/commands/`, are run: `/nk:run <name>`

That is the whole set. **Name every command that ships** - a command missing from this list is a
command nobody finds, and `help` is the only place the set is enumerated for a person.

**Where things are.** Which store resolved here, `~/.notekeeping/` for global, and whether either
exists yet. **Plus the machine's other workspaces**, from global's `## Workspaces` registry - it is
the only way to answer *what else have I got*, since resolution only ever walks up from here
(`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`).

**The shape, filled from the run and never copied from here:**

```
The loop
  <the four steps, one line>

The commands
  lifecycle   <names>
  artifact    <names>
  knowledge   <names>
  tooling     <names>

Where things are
  here        <resolved store> | <none yet>
  global      <path> | <not created>
  workspaces  <names from global's registry> | <none registered>
```

**The three sections keep this order and all three are printed** - a store that does not exist yet
is the answer to *where things are*, not a reason to drop the section.

## With an argument

**A command** - what it reads, what it writes, its flags, and when you would reach for it.

**A file** - its question, its admission test, and its **exclusion test**: where a fact goes when it
does not belong here. The exclusion test is the part people need and the part most documentation
omits. Resolve the definition through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` so you
describe the file this store actually uses, not the shipped default - and say when the answer came
from the user's own overlay.

**A term** - the definition, and the one place it is specified.

## The shape of the thing, if they ask

Knowledge moves one way: a work item promotes into a project, a project into the workspace or global.
Nothing flows back inward. Files are markdown in a directory the user owns; there is no database and
no index that cannot be deleted and rebuilt.

**Nothing is captured automatically.** There are no hooks. Every write is named by a human - which
means notes only exist if they run `/nk:save`, and it is worth saying that plainly.

## `--oneline` - the discovery surface

**`help` is the discovery surface.** A consumer asks it what it is talking to. Answer with the
contract version and the callable list, and nothing conversational. **Read the version from
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` and never state one from here.**

**Derive the list; never recite one from here.** Glob `${CLAUDE_PLUGIN_ROOT}/commands/*.md` and name
every command found - every one of them accepts `--oneline`, and under it does what it does for a
person. A list typed into this file goes stale the next time a command is added.

The shape, with the command names filled in from that glob rather than copied from here:

```
nk: help ok — contract <version>; <every command that ships>
```

That is what lets a consumer built against an older contract find out before it calls anything.
**If the derived list and this file's grouped list above disagree in length, say so** - one of them
is wrong, and that is worth a line of output rather than a silent choice between them.
