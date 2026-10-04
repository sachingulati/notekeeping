---
doc:   schema-version
title: The schema release, and what a command does when a store or a file is behind it
---

# Schema versions

**The plugin ships one schema release, it is stated here, and it is stated nowhere else.** A command
that needs the number reads this file.

| | |
|---|---|
| **The schema release this plugin ships** | **2** |

**It versions the store's shape on disk** - which files exist, what frontmatter they carry, what the
resolver's columns are, and what a config file holds. **It is not the plugin's version**, which
`plugin.json` carries: most releases change prompts and rules and leave every byte of a store valid.
**A schema release moves only when a store that already exists would have to change on disk to stay
readable.**

## Four places the number appears, in one numbering space

| Where | Says |
|---|---|
| **this file** | the release the plugin ships |
| **`schema:`** in a file definition | **the release at which this definition's shape last changed** |
| **the file stamp**, below | **the release whose shape this one written file was last written to match** |
| **`schema_version`** in a store's `config.md` | the release this store has been brought **fully** up to |

```
a file is outstanding  <=>  its stamp  <  its own definition's `schema:`
```

**A definition that never changes never moves**; most sit at `1`, and their files are never
outstanding.

## The four answers, and there are no others

| The store's `schema_version` | Answer |
|---|---|
| **equal to this file's** | proceed |
| **older** | the store predates this plugin. **Say so and name `/nk:upgrade`, then carry on** - the per-file rule below decides what is actually blocked |
| **newer** | the plugin predates the store. **Refuse, and name the plugin update.** Never downgrade a store, and never guess what an unrecognised field means |
| **absent** | **stop and ask.** An absent version is never assumed to be the current one |

## Only `/nk:upgrade` migrates

**No other command converts a file, walks the store for outstanding files, or moves
`schema_version`.** A gap reaches only a file that is itself outstanding, so a command about to write
a file asks one question of it:

| The file | The command |
|---|---|
| **new, or not outstanding** | writes it, at the current shape. A file whose definition is above 1 carries the current stamp |
| **outstanding, and this command rebuilds it whole from its sources** - `overview.md` under `/nk:project` | **rebuilds it** - a rebuild writes the current shape and its stamp, because that is what a rebuild is, not a conversion. The report names it |
| **outstanding, and this command would edit part of it** | **does not write it.** Report it as outstanding and name `/nk:upgrade` |

**Blocking the whole store on a gap that reaches one file is an error**, and so is reading the store
to find out what moved. How `/nk:upgrade` converts, and how a migration is written, are the migration rules - cited by the
skills that need them, never read here.

## The file stamp

```
<!-- nk: schema 2 -->
```

**One line, as the file's first line** - or directly below the frontmatter where the file has some -
**written in the same write as the content it describes**, never in a pass of its own. **Find it
with a search anchored at the start of the line and never at the end**, matching the version as a
number: a file whose lines end `\r\n` carries a byte before the end of the line. **An absent stamp is
release 1.** Projections, `index.md` and `config.md` carry none - they are regenerated, not migrated.
