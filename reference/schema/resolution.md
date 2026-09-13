---
doc:   resolution
title: Resolving a definition - the shipped layer, and the user's overlay
---

# Resolving a definition

**Every command that reads a file definition, or that runs a prompt, resolves it here first.** The
rule is one sentence: **the user's layer wins, whole.**

| Layer | Lives | On a plugin update |
|---|---|---|
| the user's overlay | `<store>/schema/` | **never touched** |
| the shipped default | `${CLAUDE_PLUGIN_ROOT}/reference/schema/` | replaced wholesale |

`<store>` is the resolved workspace store - `<workspace>/.notekeeping/`. If none resolved, there is no overlay to
find, and the shipped layer is the answer.

---

## A file definition

**Four scopes resolve against four directories, one each.** The ladder is work item -> project ->
workspace -> global.

| Scope | Directory |
|---|---|
| `work` | `files/work/` |
| `project` | `files/project/` |
| `workspace` | `files/workspace/` |
| `global` | `files/global/` |

For a file named `<name>.md` at scope `<scope>`, take the directory from the table, then:

1. `<store>/schema/files/<dir>/<name>.md` - use this if it exists, and use it
   **completely**.
2. `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/<dir>/<name>.md` - otherwise.

**Never merge the two.** An overlay definition replaces the shipped one field for field, including
the fields it does not mention. A half-merged admission test is worse than either version of it.

**`enabled: false` means the file does not exist for this store.** Say so, name the one line of
overlay that turns it on, and write nothing. This holds for a shipped file the user disabled exactly
as it holds for one that ships disabled.

**A definition with no `## Exclusion` section is invalid.** Report it and stop, rather than guessing
a boundary - a file with no exclusion test becomes the next catch-all. `/nk:doctor` reports it as an
error.

## A prompt overlay

`<store>/schema/prompts/<command>.md`, where it exists, **replaces the instructions the
command would otherwise follow** - what it asks, in what order, and in what house style.

**What an overlay cannot replace:** where the command may write, its `--dry-run` behaviour, the
resolved file definition's admission and exclusion tests, and the promotion rules. Those belong to
the plugin. An overlay that appears to contradict one is followed for style and refused for
substance - say which part you refused, and why.

## A user-defined command

`<store>/schema/commands/<name>.md`. It is namespaced identically to a shipped command
and behaves like one.

- **`writes:` is required**, and is validated against what the command actually touches.
- **It may write only inside a store.** Projections belong to the plugin.
- **It may not shadow a shipped command name.** Refuse at load, and say which name collided.
