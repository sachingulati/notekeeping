---
name: config
description: Show or change Notekeeping settings, and set a store file's budget, naming the file each value came from. Use when the user asks to see or change a Notekeeping setting.
argument-hint: "[set <key> <value>] [set budget <scope>/<file> <value>] [budgets]"
allowed-tools: Read, Glob, Write, Edit
---

Show or change configuration. Writes only to the config files, and to the store's schema overlay
for `set budget`.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

## Bare invocation

Print every setting, its effective value, and where that value came from - machine config,
store config, or the shipped default.

The shipped defaults are `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` for `schema_version`), and that file is
also the list of settings that exist. Read it: *every setting* means every row there, including the
ones no command has read yet. **Never state a default you did not read from it.**

The shape, filled from the run and never copied from here:

```
Settings
  <setting>               <effective value>   <origin>
  <setting>               <effective value>   <origin>
```

One row per setting in `config-defaults.md`, in that file's order, so two runs list the same
settings in the same places. A setting no command has read yet is a row like any other.

## `set`

`/nk:config set <key> <value>` writes one setting. Say which file you wrote and what the previous
value was.

Refuse an unknown key rather than writing it. **`schema_version` is refused too**: `/nk:init` writes
it and only `/nk:upgrade` moves it. `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md` is what
"known" means; refusing a key listed there would refuse a setting the product documents.

### `set budget`

`/nk:config set budget <scope>/<file> <value>` sets one file's ceiling - on its definition's
overlay, never in a config file. Before anything else under this form, read
`${CLAUDE_PLUGIN_ROOT}/skills/config/references/set-budget.md` (with
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`): where the value is written, the three cases
at that path, and the refusals.

## `budgets`

`/nk:config budgets` prints every file budget: the definition, its effective ceiling, and where
that value came from - the shipped definition, or the overlay - exactly as the bare invocation does
for settings. Each value is printed in its own unit - bytes, entries, or a notice threshold -
and a budget stated by reference as the register it points to.

The shape, filled from the run and never copied from here:

```
Budgets
  <definition>            <ceiling>           <origin>
  <definition>            <ceiling>           <origin>
```

It is a separate form because it resolves every definition; bare `/nk:config` reads two config
files and one defaults table.

## The two levels

**Machine** - `~/.notekeeping/config.md`, inside global's store. One setting, `schema_version`, plus the `## Workspaces` registry
`/nk:init` appends to - which is not a setting, and which `set` never writes.

**Store** - `<store>/config.md`, one per workspace. Everything else, so it travels with the notes:
thresholds, `schema_version`, `context_window_tokens`, tracker patterns. A
file's own budget is not here - it is on its definition, and `set budget` writes the overlay.

Both store locations are resolved, never configured. A store is `.notekeeping/` at the root of
its scope, found by walking up from the working directory; the workspace read line's target is the
directory holding that store's `.notekeeping/`. One walk produces both -
`${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`.

## Delivery

Delivery follows registration - what makes a project, a workspace or global delivered is
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, the contract for what each one writes. Nothing about
it is configurable. **A command never
alters delivery unasked**, in either direction.
