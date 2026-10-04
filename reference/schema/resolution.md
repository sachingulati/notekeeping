---
doc:   resolution
title: Resolving a definition - the shipped layer, and the user's overlay
---

# Resolving a definition

**Every command that reads a file definition resolves it here first.** A definition that gives its
shape as *the same shape as a project's* borrows it: resolve the project definition of the same file
too, and take the entry format from there. The
rule is one sentence: **the user's layer wins** - whole by default, and part by part where the
overlay says `extends:`.

| Layer | Lives | On a plugin update |
|---|---|---|
| the user's overlay | `<store>/schema/` | **never touched** |
| the shipped default | `<plugin root>/reference/schema/` | replaced wholesale |

`<store>` is the resolved workspace store - `<workspace>/.notekeeping/`. If none resolved, there is no overlay to
find, and the shipped layer is the answer.

---

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

1. `<store>/schema/files/<dir>/<name>.md` - use this if it exists.
2. `<plugin root>/reference/schema/files/<dir>/<name>.md` - otherwise.

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

**A schema upgrade does not touch the overlay either.** `/nk:upgrade` migrates the store's content
and names the overlay files a new version affects, leaving each byte-identical, as the schema-version rule says. An overlay is the user's decision, and this
layer is the one thing a plugin update is promised never to change.

**Where a migration step moves the exact part a fragment overrides, the fragment still wins, and the
report says so.** The conversion follows the **resolved** definition, as it does everywhere, so the
files move toward the shape that store actually uses. Name the fragment, name the step, and say
which part of the new shipped shape it is holding back - then leave it byte-identical. **A fragment
silently updated is the user's decision overwritten**, and a fragment silently ignored is worse: the
store would move to a shape they explicitly declined.

### What a definition carries

| Frontmatter | Means |
|---|---|
| `file` `scope` | the filename - or a directory name, written with its trailing slash - and which of the four directories it resolves from |
| `extends` | **overlay only.** `shipped` makes this file a fragment: what it does not name is inherited. Absent, the overlay replaces the shipped definition whole |
| `schema` | **the schema release at which this definition's shape last changed.** A written file is outstanding for migration when its stamp is below this - which is how a release targets the definitions it moved and reads nothing else; the schema-version rule governs it |
| `enabled` | **`false` means the file does not exist for this store.** Say so, name the one line of overlay that turns it on, and write nothing |
| `tier` | `core` - always useful; `extended` - useful to some stores. Informational; only `enabled` decides anything |
| `shape` | `document`, `document+append`, `register`, `ledger`, `container` or `bookkeeping`. A ledger is never edited; a register is appended to and corrected in place; a **container** is a directory whose files each keep the shape of the register they came from; **`bookkeeping` is the plugin's own record** - `config.md`, `index.md`, `baseline.md` - written only by the skills its definition names, **never a target for promotion or adoption, and never swept as knowledge** |
| `owner` | the command that creates or fully rebuilds the file, or `promotion`. Other commands may also write parts of it, named in the file's own definition |
| `trigger` | when it is written |
| `authority` | `original`, or `derived` - and a `derived` file must carry its refresh recipe in the header |
| `budget` | the ceiling in bytes, or the entry count that is a register's **split threshold**. `none` means uncapped. **A write that leaves the file at or past `budget_notice_pct` of it says so** - see the budget notice |
| `pairs_with` | the pair pattern, for a definition the overlay adds |

**Writing a file also writes its schema stamp**, where the definition has reached a release above 1
- one line, in the same write as the content, per
the schema-version rule. It is what lets a later migration target the
files that need it instead of reading the store.

**And where the file being written is behind its definition, the write converts it** - in that same
write, if every step between says *on write*, and the report names it. Otherwise the file is
reported as outstanding and left to `/nk:upgrade`. The rule and the declaration are
the schema-version rule's; **a command finishes the file in its hands and
never goes looking for others.**

Sections: `## Question`, `## Admission`, `## Exclusion`, `## Entry format`.

**A definition with no `## Exclusion` section is invalid.** Report it and stop rather than guessing a
boundary - a file with no exclusion test becomes the next catch-all. `/nk:doctor` reports it as an
error.

**A definition whose `schema:` is above 1 needs `## Migration`, covering every step up to it, each
declaring `on write` or `upgrade only`.** Without one a file is known to be outstanding and there is
no way to convert it. `/nk:doctor` reports a missing one as an error too.
