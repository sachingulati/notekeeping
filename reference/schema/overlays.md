---
doc:   overlays
title: Overlays - the user's layer over a shipped definition
---

# Overlays

**Read this only when the store has an overlay** - `<store>/schema/files/` holds a definition. With
none, every definition is the shipped one and nothing here applies.

**The user's layer wins** - whole by default, and part by part where the overlay says `extends:`.

| Layer | Lives | On a plugin update |
|---|---|---|
| the user's overlay | `<store>/schema/` | **never touched** |
| the shipped default | `<plugin root>/reference/schema/` | replaced wholesale |

## Replacing, or extending

**Whether the overlay replaces the shipped definition or extends it is the overlay's own
declaration**, and it is one line of frontmatter:

| The overlay | Means |
|---|---|
| **carries `extends: shipped`** | a **fragment**: every field and section it does not name is **inherited** from the shipped definition, and the ones it names are **replaced whole** |
| **carries no `extends:`** | a **whole replacement**: it stands alone, field for field, including the fields it does not mention |

**A named part is replaced, never blended.** A fragment that gives `## Admission` replaces that whole
section; it does not add a line to the shipped one. **The unit of override is the field or the
section** - a half-merged admission test is worse than either version of it, and that is what this
rule still forbids.

**Only the shipped definition of the same `file` and `scope` can be extended.** There is one overlay
path per definition, so there is one thing to extend and no chain to resolve. **An `extends:` naming
anything else is an error**: report it, name the file, and resolve nothing.

## The load-bearing fields

**Three fields are load-bearing**: `schema`, which is what makes the file's migration targetable;
`enabled`; and `budget`, which is the ceiling and a register's split threshold.

- **In a whole replacement, omitting one switches it off.** **Say which are missing and what each one
  now means for that file**, rather than filling them in from the shipped definition - the
  replacement is whole, and a field quietly restored is the merge this rule forbids. What "switched
  off" means, per field:

  | Field | Omitted from a whole replacement means |
  |---|---|
  | `schema` | the file is taken out of every migration - nothing ever converts it |
  | `enabled` | the file is taken out of the store entirely |
  | `budget` | the file is uncapped, as `budget: none` - no ceiling, no budget notice, and a register never splits |

- **In a fragment, omitting one inherits it** - a whole replacement omitting `schema:` still switches
  migration off silently for that file, which is the hazard `extends:` exists to avoid.

`/nk:doctor` reports both, and they are different findings.

## An invalid overlay

**A definition with no `## Exclusion` section is invalid.** Report it and stop rather than guessing a
boundary - a file with no exclusion test becomes the next catch-all. `/nk:doctor` reports it as an
error.

**A definition whose `schema:` is above 1 needs `## Migration`, covering every step up to it**
(the migration rules). Without one a file is known to be outstanding and there is
no way to convert it. `/nk:doctor` reports a missing one as an error too.

`pairs_with` is the pair pattern, for a definition the overlay adds.

## A schema upgrade

**A schema upgrade does not touch the overlay either.** `/nk:upgrade` migrates the store's content
and names the overlay files a new version affects, leaving each byte-identical, as the schema-version rule says. An overlay is the user's decision, and this
layer is the one thing a plugin update is promised never to change.

**Where a migration step moves the exact part a fragment overrides, the fragment still wins, and the
report says so.** The conversion follows the **resolved** definition, as it does everywhere, so the
files move toward the shape that store actually uses. Name the fragment, name the step, and say
which part of the new shipped shape it is holding back - then leave it byte-identical. **A fragment
silently updated is the user's decision overwritten**, and a fragment silently ignored is worse: the
store would move to a shape they explicitly declined.
