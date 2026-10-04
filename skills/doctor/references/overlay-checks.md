---
doc:   doctor-overlay-checks
title: Doctor's checks of the user's overlay definitions
---

# The overlay checks

**Run these only where the store has an overlay** - `<store>/schema/files/` holds a definition. A
shipped definition is checked before release, so each check here is of a file the user wrote. All
are **errors**.

- A user-defined file definition with no `## Exclusion` section.
- **An overlay definition that replaces the shipped one whole and omits a load-bearing field** -
  `schema`, `enabled` or `budget`. A whole replacement stands alone, so an omitted field
  is switched off, not inherited: `schema` takes that file out of every migration, `enabled`
  takes the file out of the store. Say which are missing and what each now means for that file,
  and repair none of them - filling one in is the merge `overlays.md` forbids. Name `extends:`
  as the remedy where inheriting was what the user meant.
- **An overlay whose `extends:` does not resolve.** The only value is `shipped`, and it extends the
  shipped definition of the same `file` and `scope`. Anything else - another overlay, a definition
  at a different scope, a name that does not exist - is an error: name the file and resolve nothing.
- **A fragment naming a field the definition format does not have.** Its table is closed
  (`resolution.md`, *What a definition carries*, plus `pairs_with` in `overlays.md`), and a typo in a fragment is silent in a way a typo
  in a whole replacement is not: the misspelt field is ignored and the inherited value stands, so
  the override the user wrote does nothing. Name the field, and the one it was probably meant to be.
- **A definition whose `schema:` is above 1 with no `## Migration` section.** The files are known to
  be outstanding and there is no way to convert them. This is the version-shaped twin of the missing
  `## Exclusion` check above.

The overlay checks resolve definitions through the definition-resolution rule and the overlay rules,
so you are checking the definition this store actually uses rather than the shipped default.
