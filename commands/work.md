---
description: Create the space for a work item. Optional - /nk:save mints one too.
argument-hint: "[what] [--requirements [path]] [--project <name>] [--id <id>] [--parent <id>] [--also <id>] [--tag <name>] [--dry-run] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git status:*), Bash(git branch:*), Bash(git log:*), Bash(git diff:*)
---

Create the space for a work item. Writes inside the store only.

**This command is optional.** `/nk:save` mints a bundle too, inferring the id and slug from the work
you have been doing. Reach for `/nk:work` when you want the space opened *up front* - requirements
written before you start, or a name you choose rather than one inferred.

**This command mints. It does not switch.** Resuming an item that already exists is `/nk:load`,
which does strictly more and writes nothing - git state, discrepancies, the parent roll-up. **If
`--id` names an item that already exists, say so and point at `/nk:load <id>`**; do not create a
second folder, and do not rewrite `requirements.md`, which is write-once plus amendments. (Only
`--id` can collide - a counter is minted from the highest existing number, so it never can.)

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: never ask - refuse naming the argument
that would satisfy it - and end with the outcome line. **With no argument and no way to ask, refuse**
rather than minting from inference alone.

**Resolve the store first**, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. Everything
below happens inside the configured store and nowhere else.

## The argument is context, never an id

**Nothing inspects `$ARGUMENTS` for an id.** Whatever is typed is context - it informs the title, the
slug and `requirements.md`, and nothing else. **`--id` is the only way to set an id**, which is what
keeps this rule with no exceptions and no pattern to misfire.

| You type | Id | The text |
|---|---|---|
| `/nk:work` | the next store counter | — title inferred from the session |
| `/nk:work fix the csv export` | the next store counter | context for title, slug, requirements |
| `/nk:work TKT-482` | the next store counter | **the same** - it is words, not a key |
| `/nk:work --id TKT-482` | `TKT-482` | — |

**A tracker key typed as the argument is plain text and is not attached to the bundle.** Link one with
`--id` when minting, or `--also` afterwards. Guessing that an argument *looks like* a ticket is the
kind of inference this design refuses everywhere else, and it would misfire on any store whose
pattern is loose.

---

## Minting

The commonest reason a note never gets written is that step one asks for a decision before there is
anything to put in it. So ask for nothing that can be read.

1. **Infer from what is already here** - what this session has discussed, the current branch, files
   touched, uncommitted changes, and the argument if one was given.
2. **Propose a title and an id.** The id is `--id` when given, otherwise the next **store counter** -
   four zero-padded digits, `max(existing numeric ids) + 1` across every bucket. The folder is
   `<id>-<slug>` either way, the slug derived from the title. Say where the folder will go.
3. **Fill `requirements.md` from the best source available** - `## Three sources` below. If
   `--requirements` was given, that material is the body. Otherwise **fetch the tracker issue only if
   the resolved id matches `tracker_id_pattern`** and an adapter is enabled - with its comments and
   sub-tasks, which is what the file is then written from. **The test is on the id, never on the
   argument**, so it fires for `--id TKT-482` and not for a key typed as prose. With neither, infer.
4. **Ask only what is missing**, briefly. With no context at all: *what are you working on, and which
   project?* With context: nothing, or one confirmation.
   **If you cannot ask, do not guess.** An unresolved project is written `project: []` and named as
   unresolved in the body - never filled in from a plausible-sounding name in the surrounding
   context. A guessed project routes promotion to the wrong scope and reports git state for a
   repository this work has nothing to do with.
   **`--project <name>` supplies the answer when nobody can be asked.** It is what a refusal names,
   so it has to exist: under `--caller` the command may not ask, and without a flag the commonest
   refusal would name no way to satisfy it. The name must already be registered in the store -
   `--project` selects a project, it never creates one.
5. **Create the bundle even if it is thin, and `requirements.md` is what makes it a bundle.**
   A thin `requirements.md` is worth more than no bundle - write what is thin, in a sentence. Write
   it per the definition resolved through
   `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`.
   **Never write empty headings, and never create the folder without the file**: a bundle with no
   `requirements.md` fails `/nk:doctor`'s first error check on the day it is made, and there is
   nothing for the next `/nk:load` to resume from. If there is genuinely nothing to write, say
   what is unknown and why. *(Measured twice, sessions 26 and 27: the folder was created empty.)*
6. Regenerate the store's `index.md` per
   `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md`. Minting is one of the few things that genuinely
   changes this file, so the new row is the point of the step.

| Flag | Does |
|---|---|
| `--requirements [path]` | write `requirements.md` from requirements you already have - a file at `path`, or with no value the material pasted into this session. **Distils and cites; never transcribes.** `## Three sources` below |
| `--project <name>` | name the project, for when the command cannot ask. Must already exist |
| `--id <id>` | override the generated id |
| `--tag <name>` | label it. **Repeatable** - `--tag a11y --tag q3`. A tag is a flat label several items share; it creates one if it is new, so check `index.md` first and reuse the existing spelling |
| `--parent <id>` | declare a container |
| `--also <id>` | add a second tracker id to an existing bundle - **never creates a folder**. The one form that acts on an item that already exists, because a bundle's identity is this command's business and nothing else's |
| `--dry-run` | show the folder, id and frontmatter that would be created, and write nothing |

---

## Three sources for `requirements.md`

The body answers one question - *what was asked, and why* - and it can be filled from three places.
They are the same operation done from different material: take something that already states the ask,
understand it, and write it into the file's format.

| Source | When |
|---|---|
| **Inference, plus one question** | the default. Thin, and says what is thin |
| **A tracker issue** | the resolved id matches `tracker_id_pattern` and an adapter is enabled |
| **`--requirements [path]`** | you already have the requirements, and hand them over |

**With a path, read that file. With no value, the requirements are what was pasted into this
session** - take the most recent block of requirement-shaped material. **If there is no such paste,
say so and ask; do not fall back to inference**, because the user has just told you a real source
exists and silently writing a thin file instead looks like it was honoured. **Under `--caller`,
`--requirements` requires a path** - a tool has no paste to point at, and guessing which part of the
context was meant is exactly what the contract forbids.

### Distil; do not transcribe

Write the ask, its rationale, its acceptance criteria and its boundaries into the file's format, and
**cite the source** - the path exactly as given, and the date you read it. Two reasons, and the
second is the load-bearing one:

- A requirements document that lives in the repository **stays there.** Copying it into the store
  makes a second copy that nothing keeps in sync, and six months later the two disagree with no way
  to tell which was read.
- **The bundle holds the understanding, not an archive.** `requirements.md` is what a cold
  `/nk:load` reads to say what was asked; a document reproduced whole is the thing you had to read
  in the first place.

What the file's own exclusion rule sends elsewhere goes there rather than into the body: how you
will do it -> `plan.md`, what you learned about the terrain -> `how.md`, what happened -> `dev.md`.

### Every statement traces to the source, and nothing is added

**This is where invention gets in.** A document states an ask and a rationale, says nothing about
acceptance criteria, and a plausible set is easy to write - it will read well, and it will be wrong
in a file whose whole value is being evidence of what was *originally* asked.

**Name the absence instead**, under the heading it belongs to: *"the source states no acceptance
criteria."* Never an empty heading, and never a criterion the source does not support. It is the
rule this command already carries for `project:` and for a thin bundle, applied to a longer input.

Say in the report **which source produced the body**, and what the source did not cover.

### It mints, like everything else here

`--requirements` is read when the bundle is created and **never afterwards**. The body is written
once - that is what makes it evidence - and a later, fuller document is an appended amendment, which
no command writes yet. An id that already exists is still a redirect to `/nk:load`, and this flag
does not change that.

**With `--id` and a fetchable tracker key, both sources are used**: the supplied document is the
body, because handing it over is deliberate, and the issue supplies frontmatter and is named as the
second source. `--dry-run` shows which source would produce the body, and cites it, without writing.

---

## Rules

- **Minting and resuming are different commands.** This one only ever creates. `/nk:load` resolves,
  reads and reports, and declares no write tool at all - so the read path cannot write by accident.
  An existing id here is a redirect, never a silent switch.
- **The folder never renames.** Ids are opaque and stable; a tracker id can be attached later with
  `--also`. Start with a counter, add the ticket when it exists.
- **A local item carries no status machinery** - no workflow, no transitions, no progress field. It
  is a name that holds notes together.
- Folder layout is `work/<YYYY-MM>/<id>/`, where the bucket is the month of first work. **An opaque
  id takes a slug: `<id>-<slug>`.** Opaque means it matches `tracker_id_pattern` or `^\d+$` - a
  tracker key and a counter both do, so both take a slug. Anything else is a name you chose, which
  already says what it is and takes none. **The test is on the id itself, not on how it arrived**:
  `--id TKT-482` takes a slug, `--id spike-auth` does not.
- **The counter is derived, never stored.** Scan every bucket for ids matching `^\d+$`, take the
  highest, add one, pad to four digits. Nothing persists, so nothing can drift - and rebuilding the
  index from the folders on disk recovers it exactly. Numbers are never reused: nothing is deleted
  here.
- **The padding is load-bearing, not cosmetic.** `0001` sorts before `0010`; `1` does not sort
  before `10`.
- **If the minted id would match this store's `tracker_id_pattern`, refuse and name the collision.**
  A bare-numeric tracker pattern would send the adapter to fetch a ticket that never existed. Say
  which setting collides rather than falling back silently.
