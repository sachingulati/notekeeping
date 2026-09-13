---
description: Checkpoint the session. Rewrites the handover, promotes what outlived the task, drains memory.
argument-hint: "[id] [--tag <name>] [--promote auto|none] [--dry-run] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git status:*), Bash(git branch:*), Bash(git log:*), Bash(git diff:*), Bash(git stash list:*)
---

The checkpoint. **Idempotent and safe to run many times per session - that is the normal usage, not
the exception.**

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: never ask - refuse naming the argument
that would satisfy it - and end with the outcome line.

`/nk:save` exists so the session becomes disposable: save, then `/clear`, and `/nk:load` restores
you. Everything below follows from that contract.

Resolve the `dev.md` and `NOTES.md` definitions per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` before writing either - the user's overlay
wins over the shipped default.

## The steps, in order

| # | Do | On failure |
|---|---|---|
| 1 | **Resolve the store first** per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, then the target inside it - **minting one if there is none** (below). State the inference and name the store path; ask only if genuinely ambiguous | **abort** |
| 2 | Rewrite the handover block in `dev.md` | **abort** |
| 3 | Append or extend **today's** session block - re-running the same day extends it | **abort** |
| 4 | Promotion check - route, scope, dedupe, classify, report verdicts, write on confirmation (or per `--promote`) | **abort** |
| 5 | Drain the harness memory store - read, promote what survives admission, clear what we wrote | **abort, and say so** |
| 6 | Refresh `NOTES.md` - the active pointer, and the verified stamp if the repo or environment moved | **abort** |
| 7 | **Settle the item's tags** - `--tag`, plus what the session named. `## Tags` below | **warn** |
| 8 | Regenerate the store's `index.md` per `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` - **a save that changed no id, title, project, parent or tag leaves it byte-identical** | **abort** |
| 9 | Regenerate projections per `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - which decides whether either is enabled, checks the markers before writing, and orders the ignore step | **warn** |
| 10 | Offer `summary.md` when the item closed or its change merged | - |
| 11 | **Is the repo you are standing in a registered project?** If not, say so and offer to initialise it - `## An unregistered repo` below | **warn** |

> **Store first, projections last.** A failed store write aborts the save. A failed projection is a
> warning, because a projection is derivable and `/nk:doctor --fix` rebuilds it.
>
> **Step 11 is last on purpose, and it is never a reason to abort.** It is about where the user is
> standing, not about what they asked to save.

## Step 1 in full - resolving, or minting

**`/nk:work` is optional, and this is what makes it optional.** A save with nowhere to write mints
the bundle itself, so you can simply work and save.

1. **`[id]` given?** Resolve it. If it exists, that is the target. **If it does not, mint it** with
   that id, exactly as `/nk:work --id` would.
2. **No `[id]`?** Infer the target from the branch, the files touched, and what the session has
   discussed - then **resolve that inference against `index.md`**, not against your own memory of it.
3. **One confident match → that is the target.** Several → list them and ask. **None → mint**, with
   the next store counter and a slug from the inferred title, and **say plainly that you are creating
   rather than resuming**.
4. **Anything short of confident → ask.** Naming the item costs one line; the alternative costs a
   duplicate.

> **The hazard is a second bundle for one piece of work**, and it is why this resolves against the
> index rather than the session. Two saves an hour apart, with the session drifted, can infer two
> slugs for the same work and open two folders - which nothing later will merge, because nothing here
> is ever deleted. `/nk:work` carries the same rule for the same reason: **never create a second
> folder for the same work.**

**Under `--caller`, mint only when `[id]` was given.** Minting from inference alone, with nobody able
to confirm and no way to ask, is precisely what the consumer contract's *refuse rather than guess*
exists to prevent - so with no argument and no resolvable target, **refuse and name `[id]` as what
would satisfy it.**

**A minted bundle gets `requirements.md` or it does not get created**, exactly as in `/nk:work` - a
folder without one fails `/nk:doctor`'s first error check on the day it is made.

Steps 2-7 are the durable invariant. If any fails, **report failure and name what was and was not
written.** Never report success on a partial run.

## The handover block

This is the single home of current state, and it is rewritten - never appended to. It carries
everything a cold session needs:

```markdown
## Where things stand
**Branch**    <branch> - <n> ahead of origin - <n> dirty files
**Done**      what is finished and verified
**In flight** what is started, and where it is
**Next**      the next decision or action
**Blocked**   what is stuck, and on whom
**Verify**    the command that proves it works
```

About fifteen lines. Longer narrative belongs in today's session block, not here. The project's
`NOTES.md` holds a **pointer** to this block, never a copy - two copies of "where things stand"
drift, and the copy is what the next session trusts.

## The drain is not optional

Anything left only in the harness memory store dies on `/clear`. **A save that cannot drain must say
so** rather than reporting success. Read that store, promote what passes an admission test, and clear
only what this plugin put there - never anything else.

## Tags

**A tag is a flat label several work items share** - a recurring effort, a theme, a push. An item
carries any number. The field is `tags:` and the resolver's column is `tags`, the same word on both
sides; `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` has the shape.

**Two sources, and both write the same field.**

| Source | Does |
|---|---|
| `--tag <name>` | **repeatable.** Adds each to the resolved item. Explicit, and never refused |
| **the session** | adds tags the session actually named - below |

### Picking tags up from the session

**Read `index.md`'s `tags` column first, and prefer what is already there.** If the session's subject
matches an existing tag, apply **that spelling**. This is the whole difference between a tag that
accumulates a thread and a tag that is a thread of one.

**A genuinely new subject may be minted**, and this is the one place this command names something the
user did not type. Three rules make that safe enough to be worth it:

1. **Never mint a variant of something that exists.** `auth` when `authentication` is in the column,
   `a11y` when `accessibility` is - **reuse the existing one.** Compare case-insensitively and on
   meaning, not just on spelling.
2. **Announce every tag, and say which are new.** *"Tags: `accessibility` (existing), `rate-limiting`
   (new)."* A tag applied silently is a thread the user cannot find later.
3. **Only what the session was actually about.** A topic mentioned once in passing is not a tag.
   The test is whether a future session looking for this work would search that word.

**Adding a tag is never destructive** - it appends to the list and removes nothing. **Removing a tag
is a hand edit**, deliberately: nothing here deletes a label the user chose.

**Under `--caller`, apply `--tag` and mint nothing.** Naming something the user did not type, with
nobody able to confirm and no way to ask, is exactly what the consumer contract refuses.

**`/nk:doctor` reports near-duplicate tags**, which is the repair path when a variant is minted
anyway.

## Promotion

For each candidate fact: does it belong in the store at all, which file does its admission test send
it to, what is the narrowest scope covering every source that taught it, and is it already there.
Report the verdicts and **write on confirmation**. Do not promote silently.

**When nobody can be asked, `--promote` is what authorises it.** A save that has something to promote
and no way to ask would otherwise be stuck: it may not ask, and it may not promote silently.

| | |
|---|---|
| *(omitted)* | report the verdicts and ask. The normal case, and the default |
| `--promote auto` | apply every verdict without asking, and list what was written |
| `--promote none` | report the verdicts and write none of them |

**`auto` is not "promote everything".** The verdicts are unchanged - a duplicate is still skipped, a
conflict is still reported rather than resolved. It authorises acting on them without a human turn.
Under `--caller`, one of the two must be given: a consumer that supplies neither gets a refusal
naming both, because guessing which one they meant is exactly the silent promotion this forbids.

## An unregistered repo

**A save resolves the work item's project; it does not ask where you are standing.** That made one
case invisible: the store resolves, the save is correct, and the repository you are in is not a
registered project of it - so nothing is ever delivered there and nothing ever says why.

**The comparison is free.** Step 1 already resolved the repo root per
`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and step 8 already read the project register.
Compare the one against the other. **Use only a root `git rev-parse --show-toplevel` returned** - if
git was unavailable, you do not know where you are, so say nothing rather than offering to register a
directory you inferred.

**Inform, then offer - once.**

> This repo - `/abs/path/web-client` - is not a registered project of this workspace, so notes saved
> here are not delivered to it. Want me to register it? That is `/nk:init` from inside it.

**If they decline, record the decline in the workspace store and do not ask again.** A scratch clone,
a vendored dependency, or a repository deliberately kept out is a legitimate standing answer, and a
prompt that returns every save teaches the user that this command's prompts are noise.

**Never register it yourself.** Initialising is `/nk:init`'s job - it resolves the workspace, refuses
`$HOME`, proposes the name and writes the projection. A save that created a project would be guessing
the name from the directory, which is the exact inference `/nk:init` exists to stop.

**Under `--caller`, report and move on.** No offer, no registration, nothing recorded: a consumer
never initialises anything (`reference/consumer-contract.md`). The status stays `ok` - the store
resolved and the item saved, so this is a detail, not a failure - and the repo rides in the detail:

```
nk: save ok - work/2026-09/spike-auth, 3 files; repo web-client not registered
```

## End by confirming it is safe to leave

Name the command that gets them back:

> Saved. Safe to `/clear` - resume with `/nk:load <id>`.
