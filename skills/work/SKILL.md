---
name: work
description: Create the record for a new work item, with requirements from a ticket, a file or the conversation. Use when the user asks to open, register or track a task or ticket in the notes.
argument-hint: "[what] [--requirements [path]] [--project <name>] [--id <id>] [--parent <id>] [--also <id>] [--tag <name>] [--dry-run]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

Create the space for a work item. Writes inside the store only.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

This command mints. It does not switch. Resuming an item that already exists is `/nk:load`,
which does strictly more and writes nothing - the branch, discrepancies, the parent roll-up. **If
`--id` names an item that already exists, say so and point at `/nk:load <id>`**; do not create a
second folder, and do not rewrite `requirements.md`, which is write-once plus amendments. (Only
`--id` can collide - a counter is minted from the highest existing number, so it never can.)

Everything
below happens inside the resolved store and nowhere else.

## The argument is context, never an id

Nothing inspects `$ARGUMENTS` for an id. Whatever is typed is context - it informs the title, the
slug and `requirements.md`, and nothing else. `--id` is the only way to set an id, which is what
keeps this rule with no exceptions and no pattern to misfire.

| You type | Id | The text |
|---|---|---|
| `/nk:work` | the next store counter | — title inferred from the session |
| `/nk:work fix the csv export` | the next store counter | context for title, slug, requirements |
| `/nk:work TKT-482` | the next store counter | the same - it is words, not a key |
| `/nk:work --id TKT-482` | `TKT-482` | — |

A tracker key typed as the argument is plain text and is not attached to the bundle. Link one with
`--id` when minting, or `--also` afterwards - guessing that an argument *looks like* a ticket would
misfire on any store whose tracker pattern is loose.

---

## Minting

Ask for nothing that can be read.

1. **Infer from what is already here** - what this session has discussed and the files it touched
   lead; then the argument if one was given, and the current branch, per
   `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md` - unknown, it is simply not an input.
2. **Propose a title and an id**, then form the folder per
   `${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md` and `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`) - which owns the id, the counter, the slug rule
   and the bucket. Say where the folder will go.
3. **Fill `requirements.md` from the best source available** - `## Three sources` below. If
   `--requirements` was given, that material is the body. Otherwise fetch the tracker issue per
   `${CLAUDE_PLUGIN_ROOT}/reference/tracker.md` - only if the resolved id matches
   `tracker_id_pattern`, never on the argument - and write the file from it. With neither, infer.
4. **Ask only what is missing**, briefly (`## Asking`). With no context at all: *what are you working on, and which
   project?* With context: nothing, or one confirmation. **If you cannot ask, do not guess** - the
   project rule and `--project <name>` are in `bundle-shape.md`.
5. **Create the bundle**, per that same file. A thin `requirements.md` is worth more than no bundle;
   an absent one means no folder.
6. **Regenerate `index.md`**, per
   `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` and `${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md`.

| Flag | Does |
|---|---|
| `--requirements [path]` | write `requirements.md` from requirements you already have - a file at `path`, or with no value the material pasted into this session. Distils and cites; never transcribes. `## Three sources` below |
| `--project <name>` | name the project, for when the command cannot ask. Must already exist |
| `--id <id>` | override the generated id - checked against `local_id_pattern` or `tracker_id_pattern` before it mints, per `bundle-shape.md`, *The id* |
| `--tag <name>` | label it. Repeatable - `--tag a11y --tag q3`. Reuse, minting and the announcement are per `${CLAUDE_PLUGIN_ROOT}/reference/tags.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md`) |
| `--parent <id>` | declare a container |
| `--also <id>` | add a tracker id to the `ids:` list of an existing bundle - **never creates a folder**. The bundle is the one `--id` names, or the one the session resolves to per `${CLAUDE_PLUGIN_ROOT}/reference/item.md` - resolve it first, and where it is ambiguous, ask instead of refusing. `[what]` is context here as everywhere, and never the key. The one form that acts on an item that already exists, because a bundle's identity is this command's business |
| `--dry-run` | show the folder, id and frontmatter that would be created, and write nothing |

---

## Three sources for `requirements.md`

The body answers one question - *what was asked, and why* - and it can be filled from three places.
They are the same operation done from different material: take something that already states the ask,
understand it, and write it into the file's format.

| Source | When |
|---|---|
| **Inference, plus one question** | the default. Thin, and says what is thin |
| **A tracker issue** | the resolved id matches `tracker_id_pattern` and this session already reaches the tracker |
| **`--requirements [path]`** | you already have the requirements, and hand them over |

With `--requirements`, or a tracker issue as the source, read
`${CLAUDE_PLUGIN_ROOT}/skills/work/references/requirements-sources.md` before writing the body - where the material
comes from, distilling it, tracing every statement to it, and using two sources at once. Inference
needs none of it.

## Asking

**Every question this skill asks follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`** - `nk:work
needs:` and the open questions.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:work`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

---

## Rules

- A work item carries no status machinery - no workflow, no transitions, no progress field. It
  is a name that holds notes together.
- The id, the counter, the slug and the folder are
  `${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md`'s, not this file's.
