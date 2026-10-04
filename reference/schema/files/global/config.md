---
file:        config.md
scope:       global
schema:      1
enabled:     true
tier:        core
shape:       bookkeeping
owner:       nk:init
trigger:     when global is created
authority:   original
budget:      none
---

## Question
What does global run with, and which workspaces exist?

## Admission
`schema_version`, the settings the user has set at global scope - only keys the config defaults
name - and the `## Workspaces` registry, the absolute path of every workspace root.

## Exclusion
Knowledge of any kind -> global's own files. A project -> its workspace store's `## Projects`, never
here. A key the config defaults do not name -> refused, never written.

## Entry format
The keys and their defaults are the config defaults' table. The `## Workspaces` line is the
store-resolution rule's shape. **No knowledge file depends on this one's layout**: it is read for
values, never swept for entries.

**`/nk:init` creates it and adds a workspace's line when one is created; `/nk:config` sets and clears
keys; `/nk:upgrade` moves `schema_version`.** Nothing else writes it. **An overlay that sets
`enabled: false` here is reported and ignored** - global does not resolve without it.
