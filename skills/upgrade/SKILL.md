---
name: upgrade
description: Move a Notekeeping store to the schema version this plugin ships, converting only what is outstanding; saves the report for --apply. Use when the user asks to upgrade the notes store's format or schema.
argument-hint: "[--apply [<run>] [all | <numbers>]] [--dry-run] [--page | --no-page]"
allowed-tools: Read, Glob, Grep, Write, Edit, Artifact, ArtifactData
---

Move a store from an older schema version to the one this plugin ships. **A proposal is a run
item** - its report, its answers, its log, and `copies/` for what a migration moves - per
`${CLAUDE_PLUGIN_ROOT}/reference/run-engine.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md`
and the work definitions it names), and it is asked about once - *Apply / Publish as a page / Change
answers / Not now* - per the report-apply rule's `## A yes applies what was shown`. An answer in
words - *"yes, but not 2"* - re-enters this skill, is written onto the run's items, and is asked about
again; nothing is applied from the conversation. `--apply` applies a run, in this session or a later
one. Nothing is written without one or the other - except in a store with nothing outstanding, whose
one write is its `schema_version` (the migration reference, *Nothing outstanding*). **One run covers
every store on the machine, from wherever it is typed** - *Every store on the machine*, below. Every
question this skill asks - the report question, a store's version where it has none, the walk -
follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`. Writes inside the stores only.

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
with an answer typed after it, and a run that question named still has an open item, **there is no
survey**: read each named run's `report.md`, write each answer onto its item, show each item's
resolved conversion, and ask the report question again - the report-apply rule's re-entry, with
the definitions as the items. Otherwise the run is fresh.

The bare command surveys, proposes the work with its counts, writes the proposal as the run's
report, and asks. *Apply* migrates exactly what the report holds; with no turn to answer in, it writes nothing -
not a stamp, not `schema_version`. `--dry-run` proposes, mints nothing and does not ask.
`--apply [<run>] [all | <numbers>]` applies a run - a named one resolved in the store you stand in;
none named, the latest upgrade run with an open item in each store on the machine - per the
report-apply rule's `## --apply`; with no selection it walks the items, one
`AskUserQuestion` each. Each item is one definition that moved and has a file outstanding, so `all` is the whole migration
and numbers are part of it; a partial migration is safe, because re-running is already how a
migration resumes. An item is stale when its definition's version, or its count of outstanding
files, has moved since the report - mark it stale and say so. **An item's fingerprint** (the run engine's
*Carried decisions*) is `<step> · <scope>/<file>`, its change part the definition's `schema:`: a
declined step comes back when the definition moves again.

The version
this plugin ships, the four answers, and the file stamp are
`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` - **read the number there and never state one
from here.**

## Every store on the machine

**The stores are global, every workspace in global's `## Workspaces` registry, and any store the
walk resolved from here that the registry lacks** - the registry is the list, per
`${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, *Knowing that other stores exist*. Each is a
separate store with its own version and its own overlay: probe that it is there, read its
`schema_version`, survey it against its own definitions, and
report it by absolute path. Where you stand changes nothing about which stores a run covers.

**One survey, one report, one question.** Each store with an item gets its own run item, minted in
that store; the report shows every store's items under its path, and the one report question answers
them all. **Each store is lettered in the report, in the order surveyed, global last**, so an item is
named by its store's letter and its number in that store's run - *not B2* - and a letter alone names
the store's whole run - *only C*, *not A*. A store left out keeps its run for a later `--apply`. A store with nothing outstanding is raised per the
migration reference's *Nothing outstanding*, and named.

A path that does not probe is reported, not repaired. It may be an unmounted volume. Say which
entries could not be reached and move on.

## What it does

Under `--apply`, *Apply* and a re-entry, go straight to step 5.

1. **Read `schema_version` from each store's `config.md`**, and compare per
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

**Each store, its version**, by absolute path - every store surveyed, said separately - and `no-change` for each one at the plugin's version. A newer store's refusal names the
plugin update; an absent version is the question in step 4, never a guess.
