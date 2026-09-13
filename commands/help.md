---
description: What each command does, what each file is for, and what every term means.
argument-hint: "[topic] [--caller <name>]"
allowed-tools: Read, Glob
---

Explain Notekeeping. `$ARGUMENTS` narrows to one topic - a command, a file, or a term.

## With no argument

Three short sections, in this order:

**The loop.** `/nk:work` to start, `/nk:save` to checkpoint, `/clear`, `/nk:load` to come back.
That is the whole product; everything else supports it.

**The commands**, grouped by family, one line each:
- *lifecycle* - `load` `save` `work`
- *artifact* - named exactly after the file each produces: `plan` `test` `summary` `how` `api`
- *knowledge* - `project` `review`
- *tooling* - `config` `doctor` `help` `index` `init` `budget`

That is the whole set. **Name every command that ships** - a command missing from this list is a
command nobody finds, and `help` is the only place the set is enumerated for a person.

**Where things are.** Which store resolved here, `~/.notekeeping/` for global, and whether either
exists yet.

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

## `--caller` - the discovery surface

**`help` is not a contract command, and this is its one exception.** A consumer asks it what it is
talking to. Answer with the contract version and the callable list, and nothing conversational:

```
nk: help ok — contract 1; work save load index doctor plan summary test how api project
```

That is what lets a consumer built against an older contract find out before it calls anything.
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` is the rule it is reporting on.
