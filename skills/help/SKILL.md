---
name: help
description: Explain how Notekeeping works - what each of its skills does, what each store file is for, what its terms mean. Use when the user asks how Notekeeping or one of its commands works.
argument-hint: "[topic] [--oneline]"
allowed-tools: Read, Glob, Grep
---

Explain Notekeeping. `$ARGUMENTS` narrows to one topic - a command, a file, or a term.

Before anything else, in order:
1. **`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
2. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`.
3. **An overlay?** If `<store>/schema/skills/help/` exists, follow `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`: a `SKILL.md` there replaces the rest of this file, and a file under its `references/` replaces the shipped reference of that name wherever this skill cites it.
4. **Infer before asking**: read what the conversation already states, and ask only what is still open.

## With no argument

Three short sections, in this order:

**The loop.** `/nk:work` to start, `/nk:save` to checkpoint, `/clear`, `/nk:load` to come back.
That is the whole product; everything else supports it. `/nk:work` is optional - a save with
nowhere to write mints the bundle itself, so you can just work and save.

The skills, each with what it does - derived, never recited from here. Grep `^description:` in
`${CLAUDE_PLUGIN_ROOT}/skills/` over `**/SKILL.md`: each match is one skill, named by its folder, and the description's
first sentence is its line. Print every match.

**Where things are.** Which store resolved here, `~/.notekeeping/` for global, and whether either
exists yet. Plus the machine's other workspaces, from global's `## Workspaces` registry - it is
the only way to answer *what else have I got*, since resolution only ever walks up from here
(`${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`).

The shape, filled from the run and never copied from here:

```
The loop
  <the four steps, one line>

The skills
  /nk:<name>  <what it does>      one line per skill the grep found, in its order

Where things are
  here        <resolved store> | <none yet>
  global      <path> | <not created>
  workspaces  <names from global's registry> | <none registered>
```

The three sections keep this order and all three are printed - a store that does not exist yet
is the answer to *where things are*, not a reason to drop the section.

## With an argument

**A command** - what it reads, what it writes, its flags, and when you would reach for it.

**A file** - its question, its admission test, and its exclusion test: where a fact goes when it
does not belong here. Resolve the definition through
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` so you describe the file this store actually
uses, not the shipped default - and say when the answer came from the user's own overlay.

**A term** - the definition, and the one place it is specified.

## The shape of the thing, if they ask

Knowledge moves one way: a work item promotes into a project, a project into the workspace or global.
Promotion moves outward; review can propose a move back, and nothing moves on its own. Files are
markdown in a directory the user owns, and every index is derived - it can be deleted and rebuilt.

Nothing is captured automatically: notes exist because `/nk:save` ran - typed, or started by Claude
when asked.

## `--oneline` - the discovery surface

`help` is the discovery surface. A consumer asks it what it is talking to. Answer with the
contract version and the callable list, and nothing conversational. **Read the version from
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` and never state one from here.**

**Derive the list, as above; never recite one from here.** Every skill it finds accepts
`--oneline`, and under it does what it does for a person.

The shape, with the names filled in from that grep rather than copied from here:

```
nk: help ok — contract <version>; <every command that ships>
```

That is what lets a consumer built against an older contract find out before it calls anything.
