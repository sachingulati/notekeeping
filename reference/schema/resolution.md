---
doc:   resolution
title: Resolving a definition - the shipped layer, and the user's overlay
---

# Resolving a definition

**Every command that reads a file definition resolves it here first.** A definition that gives its
shape as *the same shape as a project's* borrows it: resolve the project definition of the same file
too, and take the entry format from there.

## A file definition

**Four scopes resolve against four directories, one each.** The ladder is work item -> project ->
workspace -> global.

| Scope | Directory |
|---|---|
| `work` | `files/work/` |
| `project` | `files/project/` |
| `workspace` | `files/workspace/` |
| `global` | `files/global/` |

For a file named `<name>.md` at scope `<scope>`, take the directory from the table, then:

1. `<store>/schema/files/<dir>/<name>.md` - use this if it exists, under the overlay rules.
2. `<plugin root>/reference/schema/files/<dir>/<name>.md` - otherwise.

`<store>` is the resolved workspace store - `<workspace>/.notekeeping/`. **Where the store has no
overlay** - nothing under `<store>/schema/files/`, or no store resolved - step 1 never applies: take
the shipped definition and probe no overlay path.

### What a definition carries

| Frontmatter | Means |
|---|---|
| `file` `scope` | the filename - or a directory name, written with its trailing slash - and which of the four directories it resolves from |
| `extends` | **overlay only** - the overlay rules |
| `schema` | **the schema release at which this definition's shape last changed.** A written file is outstanding for migration when its stamp is below this - which is how a release targets the definitions it moved and reads nothing else; the schema-version rule governs it |
| `enabled` | **`false` means the file does not exist for this store.** Say so, name the one line of overlay that turns it on, and write nothing |
| `tier` | `core` - always useful; `extended` - useful to some stores. Informational; only `enabled` decides anything |
| `shape` | `document`, `document+append`, `register`, `ledger`, `container` or `bookkeeping`. A ledger is never edited; a register is appended to and corrected in place; a **container** is a directory whose files each keep the shape of the register they came from; **`bookkeeping` is the plugin's own record** - `config.md`, `index.md`, `baseline.md` - written only by the skills its definition names, **never a target for promotion or adoption, and never swept as knowledge** |
| `owner` | the command that creates or fully rebuilds the file, or `promotion`. Other commands may also write parts of it, named in the file's own definition |
| `trigger` | when it is written |
| `authority` | `original`, or `derived` - and a `derived` file must carry its refresh recipe in the header |
| `budget` | the ceiling in bytes, or the entry count that is a register's **split threshold**. `none` means uncapped. **A write that leaves the file at or past `budget_notice_pct` of it says so** - see the budget notice |

**Writing a file also writes its schema stamp**, where the definition has reached a release above 1
- one line, in the same write as the content, per
the schema-version rule. It is what lets a later migration target the
files that need it instead of reading the store.

**A file being written that is behind its definition is never converted on the way past** - only
`/nk:upgrade` migrates. What a command does with it is the schema-version rule's, *Only
`/nk:upgrade` migrates*.

Sections: `## Question`, `## Admission`, `## Exclusion`, `## Entry format`.
