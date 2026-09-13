---
description: Show or change settings. The one file the user edits by hand.
argument-hint: "[set <key> <value>] [--caller <name>]"
allowed-tools: Read, Glob, Write, Edit
---

Show or change configuration. Writes only to the config files.

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` - and note what it says about this command:
**resolved values are readable, and `set` is refused.** Settings are a person's act, so the detail
says that rather than naming a flag - there is no flag that would unlock it.

**Emit the outcome line and nothing else.** Explaining the refusal *above* the line is the narration
the contract forbids - the explanation belongs in `<detail>`, on the line itself.

## Bare invocation

Print every setting, its **effective value**, and **where that value came from** - machine config,
store config, or the shipped default. A setting whose origin is invisible is a setting nobody trusts.

## `set`

`/nk:config set <key> <value>` writes one setting. Say which file you wrote and what the previous
value was.

Refuse an unknown key rather than writing it - a typo that silently becomes a setting is a bug that
surfaces much later.

## The two levels

**Machine** - `~/.notekeeping/config.md`, **inside global's store.** One field: `schema_version`.

**Store** - `<store>/config.md`, one per workspace. Everything else, so it travels with the notes:
budgets, thresholds, `schema_version`, `context_window_tokens`, tracker patterns, projection
settings.

**There is no `notes_root`.** A store is `.notekeeping/` at the root of its scope and is found by
walking up from the working directory, so nothing has to record where it is. Resolution is in
`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`.

**There is no `workspace_root`.** The workspace projection is written to the directory holding that
store's `.notekeeping/`, which the same walk already resolved. Nothing records it.

## The projection settings

**Both are store-scoped. Never change any of them unasked, in either direction.**

| Setting | Default | Governs | What it costs |
|---|---|---|---|
| `projections.enabled` | **`true`** | `<repo>/CLAUDE.local.md` | bytes charged on every prompt **in that repo** |
| `projections.workspace` | **`true`** | `<workspace-root>/CLAUDE.local.md` | bytes charged on every prompt in **every** session under that root, including ones with nothing to do with these notes |

They are separate settings because the second is an order of magnitude more expensive than the first,
and **it is the one to turn off first** if the always-loaded cost bites. **Say what that costs when
asked to do it:** the repo projection does *not* pick the workspace scope up, so turning it off means
workspace facts stop being delivered (3.18). Off means off.

**The direction of the old rule reversed, and the rule did not.** This section used to read *never
turn either on unasked*, because both shipped `false`. They now ship `true`, so the same instruction
points the other way: **a command never quietly disables a projection either** - a `/nk:save` that
turned one off to save bytes would silently stop delivering the knowledge the user came for. Changing
one of these is always an explicit `/nk:config set`, and the command reports the target path when it
does.

`${CLAUDE_PLUGIN_ROOT}/reference/projections.md` is the contract for what each one writes.
