---
doc:   config-defaults
title: Every setting, and the value it has when nobody set one
---

# Config defaults

**This file is the shipped default layer.** `/nk:config` reports a setting's *effective value* and
*where that value came from* - store config, or the shipped default - and the second of those is
here.

## Resolution order

**Store config → this file.** The first hit wins, and `/nk:config` names which one it was. A setting
absent from both is absent, not zero.

**`schema_version` is the one exception**: it is read from the store being resolved - the workspace
store's own `config.md`, or global's own `config.md` when the scope being resolved is global - and
never falls back to this file. An absent version is never assumed to be the current one; it stops and
asks, per the schema-version rule.

**A `project` row is read from the store config's `## Projects` registry**, against that project's
entry, and takes no part in the order above: it is a property of one project rather than a setting
with a default to fall back to.

## The settings

| Setting | Default | Level |
|---|---|---|
| `schema_version` | **no default** - written by `/nk:init` at creation, from the schema-version rule | store (each store reads its own) |
| `tracker_id_pattern` | **none** - the pattern that recognises a tracker's issue-key format, so `--id` can tell a tracker key from a counter. Key formats differ per tracker, so there is nothing safe to assume | store |
| `local_id_pattern` | `^[a-z0-9][a-z0-9._-]*$` | store |
| `work_bucket` | `month` - `quarter` and `year` also valid | store |
| `staleness_warn_days` | `90` - days since a stamp's date, on the repo and env axes alike; the only staleness threshold | store |
| `context_window_tokens` | `1000000` | store |
| `load_depth` | `quick` - `/nk:load` reads `resume.md` and the latest session block. `full` reads all of `session.md`, which lets the next save re-derive rather than carry forward | store |
| `budget_notice_pct` | `80` | store |
| `dirs:` | **no default** - written by `/nk:init` from the repository root git gave it, or the folder the user named - never inferred. **How a project is reached from outside its folder**; a folder you stand in resolves by its read line first, per the store-resolution rule | project |
| `depends_on:` | none | project |

**`local_id_pattern` bounds a hand-chosen id, and only that.** An explicit `--id` that matches this
store's `tracker_id_pattern` is a tracker key, not a local id, and is never checked against this
setting - the two patterns test different things, and a mismatch on either is named as itself, never
as the other. Where `--id` matches neither, it is named as neither a tracker key nor a valid local
id; the bundle shape, *The id*, has the check and what follows it.

**`work_bucket` sets which period a newly minted item's folder is grouped under** - `month`,
`quarter` or `year`; the folder name each produces, and everything else about the bucket, is
the bundle shape's. **Changing it affects only items minted after
the change** - an existing item's bucket folder never moves, and every command that finds items
accepts any bucket folder, so it finds items under either shape.

**`budget_notice_pct` applies to every `budget:` in the schema** - the rule is
stated once, beside the field, in the budget-notice rule.

## Rules

**Never report a default you did not read from here.** A number that looks plausible is exactly the
failure this file exists to stop: two commands guessing in one run disagree, and neither says it
guessed.

**This table is what "known key" means.** `/nk:config set` refuses a key it does not recognise, so a
typo cannot silently become a setting. A key here is real even if no command has read it yet; a key
that is not here is not a setting.

**A file's own ceiling is not here.** Every budget and every register's split threshold lives in its
definition's `budget:` field, beside the admission test it bounds -
the file-definition rule. Say that when someone asks for a budget
setting, rather than refusing the key and stopping, and **name the form that sets one**:
`/nk:config set budget <scope>/<file> <value>`, which writes the overlay fragment so the value
survives a plugin update. `/nk:config budgets` lists them all.
