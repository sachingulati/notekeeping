---
name: doctor
description: Check the Notekeeping setup and store for what is broken, drifting or worth doing, and repair the unambiguous with --fix. Use when the user asks to check, diagnose or repair the notes setup.
argument-hint: "[--fix [<text>]] [--page | --no-page]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact
---

Check the store. Reports by default, then asks once whether to repair what is unambiguous - *Apply /
Publish as a page / Change answers / Not now*. `--fix` repairs only the unambiguous, in the same run;
nothing else writes to the store. **A run with a finding is a run item** - its report, its answers,
its log - per `${CLAUDE_PLUGIN_ROOT}/reference/run-engine.md` (with
`${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md`, `${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md` and the
work definitions it names, through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`); a clean run
mints nothing. What it needs from a repository it asks git, per
`${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`; git unavailable, each check that needs it is
reported *not checked* with the reason - never passed.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs. **Then the schema release**: read each resolved store's `schema_version` and `Grep` the release in `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (*The schema release this plugin ships*). A store at it is not mentioned; one behind it is said in one line on every run, naming `/nk:upgrade`, and the run carries on; newer or absent follows that file's four answers.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:doctor`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

**A re-entry? Decide before any check.** Where the conversation holds this skill's report question
with an answer typed after it, and the run it named still has an open finding, **no check runs**:
read that run's `report.md`, write each answer onto its finding, show each finding's resolved repair,
and ask the report question again - the report-apply rule's re-entry, with the findings as the items.
Otherwise the run is fresh.

Check only what is inside the resolved store. A store you found rather than resolved is the wrong store.

| Severity | Means |
|---|---|
| **error** | broken now - something does not work |
| **warn** | drifting - works, but degrading |
| **info** | an opportunity - a proposal you may decline |

`--fix` repairs a finding at any severity when the repair is unambiguous - a read line or the rule file
to write, an ignore entry to add, or an index row to regenerate in the shape
`${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` and `${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md` state, which every writer of that file follows.
Anything needing a judgment call is reported and left alone. Severity is not the test: it says
how bad a finding is, and
repairability says how certain the fix is. A missing ignore entry is a `warn` with an exact repair; a
store version newer than this plugin is an error with no safe repair at all.

Four prohibitions bound it whatever else is said, and none of them is a severity: **never
migrate a store**, **never lower a schema stamp**, **never merge tags**, **never remove a registry
entry.**

## The checks

**Every check - its key, its severity and what it looks for - is in
`${CLAUDE_PLUGIN_ROOT}/skills/doctor/references/checks.md`.** Read it before the first check, and
again when the conversation has been compacted since. Each check names the rule it follows; read
that rule when its check runs:

| Rule | File |
|---|---|
| the store-resolution rule | `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` |
| the resolution cases | `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md` |
| the projections rule | `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` |
| the projection-writing rule | `${CLAUDE_PLUGIN_ROOT}/reference/projections-writing.md` |
| the store-writes rule | `${CLAUDE_PLUGIN_ROOT}/reference/store/writes.md` |
| the schema-version rule | `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` |
| the migration rules | `${CLAUDE_PLUGIN_ROOT}/reference/migration.md` |
| the store walk rules | `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md` |
| the config defaults | `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md` |
| the file-facts rule | `${CLAUDE_PLUGIN_ROOT}/reference/store/file-facts.md` |
| the repo-facts rule | `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md` |
| the staleness rule | `${CLAUDE_PLUGIN_ROOT}/reference/staleness.md` |
| the drain rule | `${CLAUDE_PLUGIN_ROOT}/reference/drain.md` |
| the promotion rule | `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md` |
| the content tests | `${CLAUDE_PLUGIN_ROOT}/reference/content-tests.md` |
| the run engine | `${CLAUDE_PLUGIN_ROOT}/reference/run-engine.md` |
| `overlay-checks.md` - every `overlay-*` check, where step 2 found an overlay | `${CLAUDE_PLUGIN_ROOT}/skills/doctor/references/overlay-checks.md` |

### Findings across runs

**Every check has a key**, and a finding's fingerprint is `<key> · <subject>` - the subject
being the path, config key, tag pair, project or store it names - with no change part: the same
check on the same subject is the same finding. **A finding declined in an earlier doctor run is
carried, not reported again**, per the run engine's *Carried decisions*; the report lists what it
carried and from which runs, as items, and the arithmetic says how many - *14 findings - 9 new, 5
carried*. A carried decision that quietly absorbs a new finding is the failure this command exists
to prevent, happening inside it.

### The report question

**Before the question, the run item exists**: mint it and write its `report.md`, per the run engine -
the question names the run, and an answer to it is written there. A re-entry that finds no run
named is a fresh run, never an apply from the conversation.

Where the run's report holds a repairable finding, end with the report question -
`${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`'s `## A yes applies what was shown` - so list each
repair as what it will write, not only as what is wrong. It is asked per
`${CLAUDE_PLUGIN_ROOT}/reference/asking.md`. There is no
`--apply`:

| Pick | Do |
|---|---|
| **Apply** | the in-run repair over exactly the run's open repairable findings, as their answers leave them - read from `report.md`, never the conversation; each finding re-checked before its write, and logged |
| **Publish as a page** | the page, per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`, before anything is repaired; then ask again, without this pick |
| **Change answers** | ask again, the current answers shown |
| **Not now** | the run stays, and nothing applies it later: a later session runs `--fix` fresh |

An answer in words narrows or disambiguates, as `--fix <text>` does - so it can make a finding that
was *reported and left alone* repairable - and it is bounded exactly as that flag is, below. **It can
also decline** - *decline 7*, *decline all* - which is how a finding stops being reported. No
repairable finding, or no turn to answer in: no question - the report is the output, and the page is
offered as a line of its own where `report-pages.md` says there is enough to choose between.

### `--fix <text>`

The flag takes free text, bounded by the report this run just printed - it narrows and it
disambiguates, and never reaches past the finding list. With text, or an answer in words, follow
`${CLAUDE_PLUGIN_ROOT}/skills/doctor/references/fix-text.md`.

The report can be a page - the report question's *Publish as a page*, or the line of its own
where there is no question - per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`, which owns the
offer, the two flags, and what the page may carry. The terminal report is printed either way.

## Outside the store

**Report on nothing outside the store beyond the targets this command is given to check.** Those
targets: the three read-line targets, the `CLAUDE.local.md` files project resolution reads on the
path from the working directory up, the ignore entry, a registered project's directory - its
`AGENTS.md`, and a `CLAUDE.md` there that may import it, read for the hidden-`AGENTS.md` check - a registry
path being probed, the harness memory stores this project's directories resolve to, the harness
instructions file (read for the contradiction check and never written),
`~/.claude/settings.json` (read and added to by the read-permission repair), and **a registered
repository, through git's reads only** - the commit for staleness, *ignored* for the ignore entry -
and its exclude file, which the ignore-entry repair appends to.

**Everything else outside `.notekeeping/` belongs to the user.** Loose files, scratch, and anything
else the user put there are theirs, and none of them is a finding.
