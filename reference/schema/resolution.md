---
doc:   resolution
title: Resolving a definition - the shipped layer, and the user's overlay
---

# Resolving a definition

**Every command that reads a file definition, or that runs a prompt, resolves it here first.** The
rule is one sentence: **the user's layer wins** - whole by default, and part by part where the
overlay says `extends:`.

| Layer | Lives | On a plugin update |
|---|---|---|
| the user's overlay | `<store>/schema/` | **never touched** |
| the shipped default | `${CLAUDE_PLUGIN_ROOT}/reference/schema/` | replaced wholesale |

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
2. `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/<dir>/<name>.md` - otherwise.

**Whether the overlay replaces the shipped definition or extends it is the overlay's own
declaration**, and it is one line of frontmatter:

| The overlay | Means |
|---|---|
| **carries `extends: shipped`** | a **fragment**: every field and section it does not name is **inherited** from the shipped definition, and the ones it names are **replaced whole** |
| **carries no `extends:`** | a **whole replacement**, exactly as before: it stands alone, field for field, including the fields it does not mention |

**A named part is replaced, never blended.** A fragment that gives `## Admission` replaces that whole
section; it does not add a line to the shipped one. **The unit of override is the field or the
section** - a half-merged admission test is worse than either version of it, and that is what this
rule still forbids.

**Only the shipped definition of the same `file` and `scope` can be extended.** There is one overlay
path per definition, so there is one thing to extend and no chain to resolve. **An `extends:` naming
anything else is an error**: report it, name the file, and resolve nothing.

**Four fields are load-bearing**: `schema`, which is what makes the file's migration targetable;
`enabled`; `budget`, which is the ceiling and a register's split threshold; and `env_axis`.

- **In a whole replacement, omitting one switches it off.** **Say which are missing and what each one
  now means for that file**, rather than filling them in from the shipped definition - the
  replacement is whole, and a field quietly restored is the merge this rule forbids.
- **In a fragment, omitting one inherits it**, which is the point of the marker and the hazard it
  removes: an overlay that forgets `schema:` used to take its files out of every migration silently.

`/nk:doctor` reports both, and they are different findings.

**A schema upgrade does not touch the overlay either.** `/nk:upgrade` migrates the store's content
and names the overlay files a new version affects, leaving each byte-identical -
`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`. An overlay is the user's decision, and this
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
| `schema` | **the schema release at which this definition's shape last changed.** A written file is outstanding for migration when its stamp is below this - which is how a release targets the definitions it moved and reads nothing else. `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` |
| `enabled` | **`false` means the file does not exist for this store.** Say so, name the one line of overlay that turns it on, and write nothing |
| `tier` | `core` - always useful; `extended` - useful to some stores; `example` - a worked shape to copy. Informational; only `enabled` decides anything |
| `shape` | `document`, `document+append`, `register`, `ledger` or `container`. A ledger is never edited; a register is appended to and corrected in place; a **container** is a directory whose files each keep the shape of the register they came from |
| `owner` | the one command that writes it, or `promotion` |
| `trigger` | when it is written |
| `authority` | `original`, or `derived` - and a `derived` file must carry its refresh recipe in the header |
| `budget` | the ceiling in bytes, or the entry count that is a register's **split threshold**. `none` means uncapped. **A write that leaves the file at or past `budget_notice_pct` of it says so** - below |
| `env_axis` | whether the verified stamp's environment half is `required` or `optional` |
| `promotes_to` `promote_when` `pairs_with` `paired_from` | the pair pattern: where entries graduate to, and when |

### The budget notice

**Any command writing a file whose resolved definition carries a `budget:` says so when the write
leaves it at or past `budget_notice_pct` of that ceiling** - the share is in
`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`, and it defaults to 80.

**The rule is here rather than in each command** so that every writer inherits it by resolving the
definition, and so there is one place to change it. It covers **twenty-two definitions today**:
eight with byte ceilings, and fourteen registers whose ceiling is a **split threshold** in entries,
where 80% of 60 is 48. **`work/resume.md` is outside the rule rather than a twenty-third** - its
number is a notice, not a ceiling, so there is nothing to take 80% of, and `work/session.md` carries
no number at all.

**Count it from the definitions, never from this sentence.** The figure went stale the day three
`instructions.md` definitions shipped with a byte ceiling and nobody added them here, and the wrong
number was then quoted elsewhere as if it had been measured.

**Measure what you just wrote, not the file on disk.** The rule is about the state the write leaves
behind, and the writer is holding that content - so the byte count is the length of what it wrote
and the entry count is the entries it wrote. **Nothing needs to stat the file**, which matters
because no command's `allowed-tools` grants anything that can: a writer that goes looking for a
size it already knows either reaches for a shell it was not given, or silently skips the notice.

**Where the write was an append or an edit**, the figure is the resulting whole - what was there
plus what went in - and where that is not known exactly, **say it is approximate rather than
omitting the line.** An approximate notice is the signal; a missing one reads as *under budget*.

**One line, and it repeats.** Being over budget is a standing condition rather than an event, so the
notice appears on every write above the line and never grows past a line:

```
NOTES.md is at 10,400 of 12,000 bytes (87%)
```

**A notice is not a refusal and not a repair.** Nothing is truncated, nothing is dropped, and the
write completes - what a file does when it passes its ceiling is the definition's business, and for
a projection slice it is `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`. **Say the number, not the
remedy**: the file's own exclusion test is what decides that, and proposing a demotion here would
guess at it.

**Writing a file also writes its schema stamp**, where the definition has reached a release above 1
- one line, in the same write as the content, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`. It is what lets a later migration target the
files that need it instead of reading the store.

**And where the file being written is behind its definition, the write converts it** - in that same
write, if every step between says *on write*, and the report names it. Otherwise the file is
reported as outstanding and left to `/nk:upgrade`. The rule and the declaration are
`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`; **a command finishes the file in its hands and
never goes looking for others.**

Sections: `## Question`, `## Admission`, `## Exclusion`, `## Entry format`.
**Plus `## Migration` where `schema:` is above 1**, carrying every step up to it, **each declaring
`on write` or `upgrade only`** - without one a file is known to be outstanding and there is no way to
convert it. `/nk:doctor` reports a missing one as an error, exactly as it does a missing
`## Exclusion`.
**A definition with no `## Exclusion` section is invalid.** Report it and stop rather than guessing a
boundary - a file with no exclusion test becomes the next catch-all. `/nk:doctor` reports it as an
error.

## A prompt overlay

`<store>/schema/prompts/<command>.md`, where it exists, **replaces the instructions the
command would otherwise follow** - what it asks, in what order, and in what house style.

**A prompt overlay does not extend**, and `extends:` in one is an error to report. A definition is a
set of named fields and sections, so a part of it can be replaced and the rest inherited with nothing
left ambiguous. Instructions are read in order and depend on each other; merging half of one set into
another produces a prompt nobody wrote and nobody can predict. **Replace the whole thing, or leave
it alone.**

**What an overlay cannot replace:** where the command may write, its `--dry-run` behaviour, the
resolved file definition's admission and exclusion tests, and the promotion rules. Those belong to
the plugin. An overlay that appears to contradict one is followed for style and refused for
substance - say which part you refused, and why.

## A user-defined command

`<store>/schema/commands/<name>.md`. It is namespaced identically to a shipped command
and behaves like one.

- **`writes:` is required**, and is validated against what the command actually touches.
- **It may write only inside a store.** Projections belong to the plugin.
- **It may not shadow a shipped command name.** Refuse at load, and say which name collided.
