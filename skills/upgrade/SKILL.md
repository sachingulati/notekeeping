---
name: upgrade
description: Move a Notekeeping store to the schema version this plugin ships, converting only what is outstanding; saves the report for --apply. Use when the user asks to upgrade the notes store's format or schema.
argument-hint: "[--apply [<report>] [all | <numbers>]] [--dry-run] [--page | --no-page]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact
---

Move a store from an older schema version to the one this plugin ships. Every proposal is saved as
a report, and then asked about once - *Apply / Apply and publish / Change answers / Not now* - per
the report-apply rule's `## A yes applies what was shown`. An answer in
words - *"yes, but not 2"* - re-enters this skill, is written into the report, and is asked about
again; nothing is applied from the conversation. `--apply` applies a saved report, in this
session or a later one. Nothing is written without one or the other. Every question this skill
asks - the report question, the store's version where it has none, another workspace's
confirmation, the walk - follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`. Writes inside the store
only.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:upgrade`, with the arguments this run was given, unchanged. Nothing before it - no read, no
write, no reply. That run, not you, carries out the answer. This holds while this file's text is
still in the conversation, and when the command was typed.

**A re-entry? Decide before any survey.** Where the conversation holds this skill's report question
with an answer typed after it, and the report that question named has no `## Applied`, **there is no
survey**: read that report, append the answer as `## Answers` keyed by item number, show each item's
resolved conversion, and ask the report question again - the report shape's rules, with the
definitions as the items. Otherwise the run is fresh.

The bare command surveys, proposes the work with its counts, saves the proposal as a report, and
asks. *Apply* migrates exactly what the report holds; with no turn to answer in, it writes nothing -
not a stamp, not `schema_version`. `--dry-run` proposes, saves nothing and does not ask.
`--apply [<report>] [all | <numbers>]` applies a saved report - the latest when none is named -
per the report shape's *The report is saved*; with no selection it walks the items, one
`AskUserQuestion` each. Each item is one definition that moved, so `all` is the whole migration
and numbers are part of it; a partial migration is safe, because re-running is already how a
migration resumes. An item is stale when its definition's version, or its count of outstanding
files, has moved since the report - skip it and say so.

The version
this plugin ships, the four answers, and the file stamp are
`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` - **read the number there and never state one
from here.**

## Two stores, two versions

Global and the workspace store are separate stores and carry separate versions. Both resolve from
where you are standing, so check both and report both, and migrate each on its own answer.

Then name the machine's other workspaces. Global's `## Workspaces` registry lists them -
`${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, *Knowing that other stores exist*. For each one that is not the store you resolved:
probe that its store is actually there, read its `schema_version`, and report where it stands.

Offer to migrate them, one `AskUserQuestion` each, by absolute path. A store you are not standing
in is a bigger action than the one you are, so it is never folded into the first question and never
assumed. Each store migrated gets its own report, saved in that store's `tmp/`, and its own
report question. Decline is a normal answer - each is reachable by running this command from inside it.

A path that does not probe is reported, not repaired. It may be an unmounted volume. Say which
entries could not be reached and move on.

## What it does

Under `--apply`, *Apply* and a re-entry, go straight to step 5.

1. **Read `schema_version` from each resolved store's `config.md`**, and compare per
   `schema-version.md`'s table.
2. **Equal, in both?** Report `no-change` and write nothing. This is the ordinary outcome and it
   should cost nothing to ask for.
3. **Newer than the plugin?** **Refuse**, and name the plugin update. Never downgrade, and never
   rewrite a field you do not recognise.
4. **Absent?** **Stop and ask** which version the store was written at - one `AskUserQuestion`, the
   releases this plugin knows as its options. Do not assume it is the current one, and do not assume
   it is the oldest.
5. **Older - or `--apply`, *Apply* or a re-entry?** Follow
   `${CLAUDE_PLUGIN_ROOT}/skills/upgrade/references/migrate.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/migration.md`,
   `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`, `${CLAUDE_PLUGIN_ROOT}/reference/report-apply.md`,
   `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`, `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`, `${CLAUDE_PLUGIN_ROOT}/reference/overview.md`,
   `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`, `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` and
   `${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md`) - the survey, the proposal and its report, the conversion, and
   what a migration never touches.

## Report - a store with nothing to do

**Each store, its version**, by absolute path - the resolved workspace store and global, said
separately - and `no-change` for each one at the plugin's version. A newer store's refusal names the
plugin update; an absent version is the question in step 4, never a guess.

## Never

- **Never skip a version**, and never migrate a store this command neither resolved nor probed from the
  `## Workspaces` registry and had confirmed by its absolute path.
- **Never downgrade**, and never write into a store whose version is newer than the plugin's.
- **Only this command migrates** - surveys, converts, or moves `schema_version`. Every other command
  leaves an outstanding file to it, per `schema-version.md`.
- **Never stamp a file you did not convert**, and never stamp in a pass of its own. A stamp that does
  not travel with its change is a claim that a file was migrated when it was not.
- **Never lower a stamp**, and never convert a file stamped at or above its definition's version. A
  stamp raised by hand freezes that file out of migration - that is `/nk:doctor`'s finding to report,
  not this command's to overrule.
- **Never move `schema_version` while any definition has an outstanding file.**
- **Never rewrite the overlay**, and never delete anything a step did not name.
- Never write outside the store.
