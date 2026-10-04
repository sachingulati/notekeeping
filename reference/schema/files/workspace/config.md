---
file:        config.md
scope:       workspace
schema:      1
enabled:     true
tier:        core
shape:       bookkeeping
owner:       nk:init
trigger:     when the store is created
authority:   original
budget:      none
---

## Question
What does this store run with, and which projects does it hold?

## Admission
`schema_version`, the settings the user has set - only keys the config defaults name - and the
`## Projects` registry, one line per registered project.

## Exclusion
Knowledge of any kind -> the scope's own files. A file's budget -> that file's definition, through
`/nk:config set budget`. A key the config defaults do not name -> refused, never written.

## Entry format
The keys and their defaults are the config defaults' table; a key left out takes its default. The
`## Projects` line is the store-resolution rule's shape. **No knowledge file depends on this one's
layout**: it is read for values, never swept for entries.

**`/nk:init` creates it; `/nk:config` sets and clears keys; `/nk:init` registering a project writes
its `## Projects` line; `/nk:upgrade` moves `schema_version`.** Nothing else writes it. **An overlay
that sets `enabled: false` here is reported and ignored** - a store does not resolve without it.
