---
description: Show or change settings. The one file the user edits by hand.
argument-hint: "[set <key> <value>] [set budget <scope>/<file> <value>] [budgets] [--caller <name>]"
allowed-tools: Read, Glob, Write, Edit
---

Show or change configuration. Writes only to the config files.

**`--caller <name>`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: **resolved
values are readable, and `set` is refused.** Settings are a person's act, and the detail says so
rather than naming an argument that would satisfy it.

## Bare invocation

Print every setting, its **effective value**, and **where that value came from** - machine config,
store config, or the shipped default. A setting whose origin is invisible is a setting nobody trusts.

**The shipped defaults are `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`, and that file is
also the list of settings that exist.** Read it: *every setting* means every row there, including the
ones no command has read yet. **Never state a default you did not read from it.**

**The shape, filled from the run and never copied from here:**

```
Settings
  <setting>               <effective value>   <origin>
  <setting>               <effective value>   <origin>
```

**One row per setting in `config-defaults.md`, in that file's order**, so two runs list the same
settings in the same places. A setting no command has read yet is a row like any other.

## `set`

`/nk:config set <key> <value>` writes one setting. Say which file you wrote and what the previous
value was.

Refuse an unknown key rather than writing it - a typo that silently becomes a setting is a bug that
surfaces much later. **`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md` is what "known" means**;
refusing a key listed there would refuse a setting the product documents.

### A file's budget is not a setting, and `set` writes it anyway

**A file's ceiling lives on its definition**, beside the admission test that decides what earns a
place in it - `budget:` in
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`, *What a definition carries*. A number kept
away from its rule drifts from it, and the two are read together or not at all. So the config files
hold no file budgets and `config-defaults.md` lists none.

**`/nk:config set budget <scope>/<file> <value>` sets one**, where `<scope>` is `work`, `project`,
`workspace` or `global` - `set budget work/requirements.md 12000`. It writes the **overlay**, at
`<store>/schema/files/<scope>/<file>`, because the shipped definition is replaced wholesale on every
plugin update and a number written there would not survive one.

**What it writes depends on what is already at that path**, and there is only ever one file there:

| At `<store>/schema/files/<scope>/<file>` | Write |
|---|---|
| **nothing** | a **fragment** - `extends: shipped` and `budget:`, and nothing else |
| **a fragment** | set `budget:` in it, leaving every other line alone |
| **a whole replacement** (no `extends:`) | set `budget:` in it, and **say that this file is a whole replacement** - the value now sits in a definition that does not inherit, which is the user's own arrangement and not this command's to convert |

**Never create a second overlay for one definition**, and never convert one form to the other. Show
the diff either way, and name the file by absolute path.

**Refuse a budget key whose definition does not resolve**, and refuse one whose resolved definition
carries no `budget:` field - an uncapped file is uncapped by design, and a ceiling invented for it is
a rule nobody wrote. Say which, and stop.

## `budgets`

`/nk:config budgets` prints every file budget: the definition, its effective ceiling, and **where
that value came from** - the shipped definition, or the overlay - exactly as the bare invocation does
for settings.

**The shape, filled from the run and never copied from here:**

```
Budgets
  <definition>            <ceiling>           <origin>
  <definition>            <ceiling>           <origin>
```

**It is a separate form because it costs a separate walk.** Bare `/nk:config` reads two config files
and one defaults table; this resolves every definition to read one field from each. **Bare stays
cheap, and this pays for itself when asked.**

## The two levels

**Machine** - `~/.notekeeping/config.md`, **inside global's store.** One field: `schema_version`.

**Store** - `<store>/config.md`, one per workspace. Everything else, so it travels with the notes:
thresholds, `schema_version`, `context_window_tokens`, tracker patterns, projection ceilings. **A
file's own budget is not here** - it is on its definition, and `set budget` writes the overlay.

**Both store locations are resolved, never configured.** A store is `.notekeeping/` at the root of
its scope, found by walking up from the working directory; the workspace projection's target is the
directory holding that store's `.notekeeping/`. One walk produces both -
`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`.

## Projections

**Delivery follows registration**: a registered project is delivered, and a workspace store with
content in its `NOTES.md` is delivered. What is configurable is the cost -
`projection_project_bytes` and `projection_workspace_bytes` bound the two files separately.
**A command never alters delivery unasked**, in either direction.
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md` is the contract for what each one writes.
