---
doc:   config-defaults
title: Every setting, and the value it has when nobody set one
---

# Config defaults

**This file is the shipped default layer.** `/nk:config` reports a setting's *effective value* and
*where that value came from* - machine config, store config, or the shipped default - and the third
of those is here. **Without it a command asked for a default has nothing to read and will invent
one.**

## Resolution order

**Store config → machine config → this file.** The first hit wins, and `/nk:config` names which one
it was. A setting absent from all three is absent, not zero.

**A `project` row is read from the store config's `## Projects` registry**, against that project's
entry, and takes no part in the order above: it is a property of one project rather than a setting
with a default to fall back to.

## The settings

| Setting | Default | Level |
|---|---|---|
| `schema_version` | **no default** - written by `/nk:init` at creation, from `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` | store + machine |
| `tracker_id_pattern` | **none** - key formats differ per tracker, so there is nothing safe to assume | store |
| `local_id_pattern` | `^[a-z0-9][a-z0-9._-]*$` | store |
| `month_bucket` | `YYYY-MM` | store |
| `ignore_dirty` | **none** - no path is excluded from dirty-file reporting | store |
| `staleness_warn_commits` | `50` | store |
| `staleness_warn_days` | `90` | store |
| `context_window_tokens` | `1000000` | store |
| `load_depth` | `quick` - `/nk:load` reads `resume.md` and the latest session block. `full` reads all of `session.md`, which lets the next save re-derive rather than carry forward | store |
| `budget_notice_pct` | `80` | store |
| `projection_bytes` | `26000` | store |
| `dirs:` | **no default** - written by `/nk:init` from `git rev-parse --show-toplevel`, never inferred | project |
| `depends_on:` | none | project |

**`staleness_warn_commits` and `staleness_warn_days` are an either-trips pair** - a knowledge file
is stale when it passes *either* threshold, not both.

**`projection_bytes` is a ceiling, and what happens at a ceiling is not a default** -
a slice past its ceiling degrades to a pointer whole, and that rule lives in
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`. Only the numbers are here.

**One ceiling bounds all three projections, sized for the largest.** A `NOTES.md` is 12000 bytes
and an `instructions.md` is 2000 at every scope; global adds `environment.md` at 6000. One number
sized for global's three sources holds the other two with room to spare, and a second number would
only let one of them fall below what its sources may produce.

**A projection ceiling is never below what its sources may produce, and that is the whole rule.**
It is a **sum**: 12000 plus 6000 plus 2000, with headroom for the generation header, the fixed
line and the area lines, is **26000**. A user who fills every source to its ceiling does not silently
lose the end of one on the way into the projection, which is the defect this rule exists to prevent.

**Instructions are not budgeted against the facts, and raising these is what makes that true.** At
the old ceilings an instruction could only be afforded by a `NOTES.md` that was under its own
budget, so the two competed for one number and the trade was invisible at write time. Each source
now has its own ceiling and the projection holds them all.

**They are not one pool.** The 26000 here measures one rendered slice; the 12000 on the
definition measures that scope's `NOTES.md` across all three of its sections, so `## Active` eats
into the source budget while contributing nothing to this one. **Neither sees content hand-written
above the block** in a `CLAUDE.local.md`.

**All three projections can load in one session**, so the worst case a session pays is the ceiling
three times - 78000 bytes, about 19500 tokens. That is a ceiling rather than an observed size: the
largest block any run has produced is under 1600 bytes, and a projection costs what its sources hold.

**`budget_notice_pct` applies to every `budget:` in the schema, not to projections** - the rule is
stated once, beside the field, in `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`.

## Two rules that make this file worth having

**Never report a default you did not read from here.** A number that looks plausible is exactly the
failure this file exists to stop: two commands guessing in one run disagree, and neither says it
guessed.

**This table is what "known key" means.** `/nk:config set` refuses a key it does not recognise, so a
typo cannot silently become a setting. A key here is real even if no command has read it yet; a key
that is not here is not a setting.

**A file's own ceiling is not here.** Every budget and every register's split threshold lives in its
definition's `budget:` field, beside the admission test it bounds -
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`. Say that when someone asks for a budget
setting, rather than refusing the key and stopping, and **name the form that sets one**:
`/nk:config set budget <scope>/<file> <value>`, which writes the overlay fragment so the value
survives a plugin update. `/nk:config budgets` lists them all.
