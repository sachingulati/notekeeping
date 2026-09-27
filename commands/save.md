---
description: Checkpoint the session. Rewrites the handover, promotes what outlived the task, drains memory.
argument-hint: "[id] [--project <name>] [--tag <name>] [--dry-run] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git rev-parse:*), Bash(git status:*), Bash(git branch:*), Bash(git log:*), Bash(git diff:*), Bash(git -C:*)
---

The checkpoint. **Idempotent and safe to run many times per session - that is the normal usage, not
the exception.**

**`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

`/nk:save` exists so the session becomes disposable: save, then `/clear`, and `/nk:load` restores
you. Everything below follows from that contract.

Resolve the `resume.md`, `session.md` and `NOTES.md` definitions per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` before writing any of them - the user's overlay
wins over the shipped default.

**Steps 2 and 3 are migration-on-write carriers.** Where the resolved definition's `## Migration`
declares a step **on write**, convert the file and stamp it in the same write, and name the
conversion in the report - `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`. Where it declares
**upgrade only**, do not convert and do not write that file: report it as outstanding and name
`/nk:upgrade`. Never walk the store for files you were not already writing, and never move
`schema_version`.

**Where an `upgrade only` step reaches a file step 2 or 3 writes, the whole save stops, and the
report says so in those words** - that step aborts, and every later step is about work that did not happen. No
shipped definition is in that state today, both work-item files this command writes being at release
1; the rule stands for the release that changes one. Name it before anything else in the report, and
**say plainly not to `/clear` on the strength of that save**.

## The steps, in order

| # | Do | On failure |
|---|---|---|
| 1 | **Resolve the store first** per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, then the target inside it - **minting one if there is none** (below). State the inference and name the store path; ask only if genuinely ambiguous | **abort** |
| 2 | Rewrite `resume.md` - the position, and the story below it. `## resume.md` below | **abort** |
| 3 | Append or extend **today's** `## session <date>` block in `session.md` - re-running the same day extends it | **abort** |
| 4 | Promotion check - route, scope, dedupe, classify, write every verdict that writes, then report what was written | **abort** |
| 5 | Drain the harness memory store - read it, promote what survives admission, **clear what was promoted**, and report what could not be placed | **abort, and say so** |
| 6 | Refresh `NOTES.md` - the active pointer, and the verified stamp if the repo or environment moved | **abort** |
| 7 | **Settle the item's tags** - `--tag`, plus what the session named. `## Tags` below | **warn** |
| 8 | Regenerate the store's `index.md` per `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` - **a save that changed no id, related id, title, project, parent or tag leaves it byte-identical** | **abort** |
| 9 | Regenerate projections per `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` - the active item's project, the workspace and global; that file decides whether each has anything to render, checks the markers before writing, and orders the ignore step | **warn** |
| 10 | **Where the session says the work is done, write the closing block** - one dated heading and one line of reason, shape fixed in `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/session.md`, *Closing, and staying open*. **Only on what the user said**, never on your own reading of the work | **warn** |
| 11 | **Is the repo you are standing in a registered project?** If not, say so and offer to initialise it - `## An unregistered repo` below | **warn** |

> **Store first, projections last.** A failed store write aborts the save. A failed projection is a
> warning, because a projection is derivable and `/nk:doctor --fix` rebuilds it.
>
> **Step 11 is last on purpose, and it is never a reason to abort.** It is about where the user is
> standing, not about what they asked to save.

## Step 1 in full - resolving, or minting

**`/nk:work` is optional, and this is what makes it optional.** A save with nowhere to write mints
the bundle itself, so you can simply work and save.

**What a mint produces is `${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md`** - the id, the counter,
the slug, the folder, `requirements.md`, the project. **Follow it; this command decides only
whether to mint, never what a bundle looks like.** `/nk:work` mints against the same file, so the
two produce identical bundles.

1. **`[id]` given?** Resolve it. If it exists, that is the target. **If it does not, mint it** with
   that id.
2. **No `[id]`?** Infer the target from the branch, the files touched, and what the session has
   discussed - then **resolve that inference against `index.md`**, not against your own memory of it.
3. **One confident match → that is the target.** Several → list them and ask. **None → mint.**
4. **Anything short of confident → ask.** Naming the item costs one line; the alternative costs a
   duplicate.

**No `index.md`?** It is derived, and a store that has never completed a save has none - so its
absence is not an answer about the work. **List `work/<YYYY-MM>/` and resolve against that**, which
is the same set the index is built from, and **say you resolved against the directory**. `/nk:load`
already falls back this way; a checkpoint that refused where a load succeeded, on the same store,
would be the asymmetry rather than the caution. **Regenerate the index at step 8 as usual** - that
is what stops the fallback being needed twice.

**`--project <name>` applies to a mint only.** It names the project of a bundle being created; it
never retargets one that already exists. **`--dry-run`** reports the target it resolved or would
mint, and every file it would touch, and writes nothing.

Steps 2-6 are the durable invariant. If any fails, **report failure and name what was and was not
written.** Never report success on a partial run.

## `resume.md`

**This is the single home of current state and of the story behind it**, and it is what a cold
session reads first. Its regions and their rules are the definition's -
`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/resume.md` - and are not restated here. What this
command owes it is **how** it is rewritten.

**`## Where things stand` is regenerated every save; everything below it accumulates.** The position
is only ever about now, so it is written fresh. The story, the decisions and the rejected list are
extended, and **an entry already in the rejected list is never dropped** - reworded if it is wrong,
never removed while the item is open.

**About fifteen lines for the position.** Longer narrative belongs in today's session block, not in
that region. The project's `NOTES.md` holds a **pointer** to this file, never a copy - two copies of
"where things stand" drift, and the copy is what the next session trusts.

### Re-derive it where you can, carry it forward where you cannot

| This session loaded | Rebuild the story from | `Covers` says |
|---|---|---|
| **all of `session.md`** (a `--full` load) | **the record, which is already in context** | `re-derived` |
| the latest block only (a `--quick` load) | the previous `resume.md`, this session, and that block | `carried forward` |

**Re-deriving is what stops the file drifting**, because each rebuild is anchored to an append-only
record rather than to the last rebuild - ten carried-forward saves is ten compressions of a
compression. **Where the history is in context it costs nothing**, which is the whole reason
`--full` exists.

**Never read `session.md` to re-derive.** A save happens when the context is already filling, a
second read appends a second copy at full price, and pulling history in at that moment risks
compacting the very material being summarised. If this session did not load it, carry forward and
say so - that is what `carried forward` is for, and `/nk:doctor` is what notices a run of them.

**The dirty count excludes the paths `ignore_dirty` names**, exactly as `/nk:load` reports it
(`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`; unset means exclude nothing). **One field,
one definition** - this is the number `/nk:load` reads back, and two commands counting it
differently would make the handover disagree with the session that restored it.

## The drain is not optional

Anything left only in the harness memory store dies on `/clear`. **A save that cannot drain must say
so** rather than reporting success. Read that store, promote what passes an admission test, and
**clear what was promoted**.

**Empty and unread are different answers, and only one of them is ever inferred.** *Empty* is a
listing that returned nothing; *unread* is everything else - the directory was not found, the glob
failed, a file would not parse. **Report `empty` only from a listing you actually got back**, and
otherwise say the store could not be read and name the path you tried.

**This is the failure that costs the most and shows the least.** A drain that reports an empty store
it never read is indistinguishable, in the report, from one that found nothing - and it is followed
by the line that tells the user to `/clear`. Reporting items as absent when they were merely
unreachable is the same defect stated the other way round, and is worse than saying nothing.

**Never end a save with the safe-to-clear line when the drain did not complete.** Say what is still
only in memory, or that you could not tell, and say plainly that clearing now would lose it.

**Clearing is bounded by what was filed, not by who wrote it.** An item whose content now lives in
the store is a second copy of something already kept, and removing it is the same trim the rest of
the design performs - bounded by the content existing somewhere else. **An item that was not
promoted is not cleared**, whatever it looks like and whoever appears to have written it: the drain
never removes something whose only copy it is.

**This is one store, for one directory.** The harness keys memory by a slug derived from the working
directory, so the store a save can see belongs to the directory it ran in - not to the project, and
not to every project. **Name the store path in the report.** Where the repository's own slug and the
one in use differ - a save run from a subdirectory, or a project that has moved - a second store
holds items this run never saw, and saying so is what stops them being counted as drained.
`/nk:doctor` carries the finding.

### Finding it

**`~/.claude/projects/<cwd-slug>/memory/`**, where `<cwd-slug>` is the **absolute working directory
with every path separator, drive colon and dot replaced by `-`** - so a drive-letter root, whose
colon is followed by a separator, yields two dashes where they meet. **Glob it; do not shell out for
it.** `Glob` and `Read` reach that path on their own, and this command is granted both.

**Glob `~/.claude/projects/*/memory/*.md` and match the slug** rather than composing the path blind:
the listing is what tells you whether the directory exists at all, and matching against it is also
how the sibling-store finding above is spotted.

**A relocated `CLAUDE_CONFIG_DIR` moves this and cannot be read from here.** Nothing in this
command's grant reads an environment variable, so where `~/.claude/projects/` does not exist,
**say the store could not be located and name the variable** - never that there was nothing in it.

**What cannot be placed is reported, never cleared.** One line per item, with where it would go if
you agree. **An instruction goes to the `instructions.md` of the scope it holds at** - the item, an
area, the project, the workspace - and an instruction that holds everywhere goes to global's,
`~/.notekeeping/instructions.md`, which is projected like the others. **Never write your harness
instructions file**: an instruction is filed in a store or not at all.

## Tags

**A tag is a flat label several work items share** - a recurring effort, a theme, a push. An item
carries any number. The field is `tags:` and the resolver's column is `tags`, the same word on both
sides; `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` has the shape.

**Two sources, and both write the same field.**

| Source | Does |
|---|---|
| `--tag <name>` | **repeatable.** Adds each to the resolved item. Explicit, and never refused - but check `index.md` first and reuse the existing spelling, as `/nk:work --tag` does, and say which spelling was used |
| **the session** | adds tags the session actually named - below |

**Write the tag into the item's `requirements.md` frontmatter, and only then regenerate the index.**
`tags:` lives in `requirements.md`; `index.md`'s `tags` column is **derived from it** and is
authoritative over nothing. **A tag written to the index alone is lost at the next rebuild** - which
is what `/nk:index` will correctly report and correctly discard.

**This is an amendment, and amendments are allowed.** `requirements.md` is *write-once plus
amendments*: what `/nk:work` forbids is rewriting the bundle, not adding a label to it. Touch the
`tags:` key and nothing else in the file.

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

**`/nk:doctor` reports near-duplicate tags**, which is the repair path when a variant is minted
anyway.

## Promotion

For each candidate fact: does it belong in the store at all, which file does its admission test send
it to, what is the narrowest scope covering every source that taught it, and is it already there.
**Apply the verdicts, then report every one of them** - what was written, where, and what was
skipped or held back and why. Promotion does not wait for a yes: the report is how you see it, and a
wrong entry is corrected like any other. `--dry-run` shows the verdicts and writes nothing.

**Under `--oneline` nobody sees that report**, so every entry this run promotes carries `unseen` as
the last part of its provenance stamp - `(0012 - 2026-09-28 - unseen)`. It is the one thing
`--oneline` changes in what is written, and it is there so an entry no person has read never looks
like one someone curated. **Reading it is what clears it**: whoever has read the entry deletes the
word by hand. `/nk:doctor` counts what is still unseen.

**A scope the user named is the scope.** Where the session shows the user asking for a fact to go to
a particular level - *put this in the workspace notes*, *this is global* - route it there and propose
no other, even where the narrowest-scope rule would pick a nearer one. That is the user's decision,
not a candidate for correction. **A topic the user named narrows the candidates** - *save what we
learned about deployments to the workspace* promotes everything the session taught about that topic,
across whichever files it belongs in. The verdicts still apply inside that scope: already there is a
duplicate, a conflict is still reported.

**Applying is not "promote everything".** A duplicate is skipped, and a contradiction is reported
and never written - which of two claims is true is the user's call.

## An unregistered repo

**A save resolves the work item's project; it does not ask where you are standing.** One case turns
on the difference: the store resolves, the save is correct, and the repository you are in is not a
registered project of it - so nothing is ever delivered there and nothing ever says why.

**The comparison is free.** Step 1 already resolved the repo root per
`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and step 9 already read the project register.
Compare the one against the other. **Use only a root `git rev-parse --show-toplevel` returned** - if
git was unavailable, you do not know where you are, so say nothing rather than offering to register a
directory you inferred.

**Infer before offering anything.** The working directory is **not** the project: a user can be
standing anywhere and still be working on something registered, and then there is nothing to say.
Resolve the work item's `project:` first, then the repo root against the register. **Only a repo
that is neither is the case this section is about.**

**Then offer what is actually needed**, which is not always registration:

| Where the repo root sits | The offer |
|---|---|
| **inside the workspace root**, unregistered | register it - `/nk:init` from inside it |
| **above the workspace root** | **a project cannot be registered there at all**, so offer to initialise a workspace there, or to point the work item at a project that exists. `/nk:init` would refuse a yes, so never offer one |

> This repo - `/abs/path/web-client` - is not a registered project of this workspace, so notes saved
> here are not delivered to it. Want me to register it? That is `/nk:init` from inside it.

**Ask every time, and record nothing.** A remembered *no* outlives the thing that would have made
the question stop - the repo gets registered, or the work item gets its project, and a stored decline
goes on suppressing a line that is now correct, with nothing anywhere to clear. **A session-scoped
memory is the worst of both**: it still outlives the fix, and it leaves nothing behind to find.

**What makes asking every time bearable is that inference usually answers it first, and that this is
one line, last, and never a reason to abort.** Keep it to one line; a prompt that returns every save
teaches the user that this command's prompts are noise, and that is the cost being accepted here.

**Never register it yourself.** Initialising is `/nk:init`'s job - it resolves the workspace, refuses
`$HOME`, proposes the name and writes the projection. A save that created a project would be guessing
the name from the directory, which is the exact inference `/nk:init` exists to stop.

**Under `--oneline`, make no offer** - an offer is a question with nowhere to go
(`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`) - and record nothing. The status stays `ok` - the store
resolved and the item saved, so this is a detail, not a failure - and the repo rides in the detail:

```
nk: save ok — work/2026-09/spike-auth, 3 files; repo web-client not registered
```

## End by confirming it is safe to leave

Name the command that gets them back. **`/nk:load` needs no argument** - it infers from the branch
and the files touched, both of which survive the clear.

> Saved. Safe to `/clear` - resume with `/nk:load`.

**This line is a claim about the drain, and it is earned rather than printed.** It says nothing
survives only in the session, so it may be written **only where step 5 completed** - the store was
listed, and everything it held was either promoted or reported as still in it.

**Where the drain did not complete, say what is at risk instead** - one line, in place of this one,
never alongside it:

> Saved, but the memory store could not be read (`<path>`). Do not `/clear` yet: anything in it
> exists nowhere else.
