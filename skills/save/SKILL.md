---
name: save
description: Checkpoint the current session into the work item's (the task's) record - where things stand, what was learned, the harness memory - so it survives /clear. Use when the user asks to save, checkpoint, wrap up, or keep what this session found.
argument-hint: "[id...] [--project <name>] [--tag <name>] [--dry-run]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

The checkpoint. Idempotent and safe to run many times per session - that is the normal usage, not
the exception.

Before anything else, in order:
1. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` - a case it names in *italics* is in `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve-cases.md`, read when it occurs. **Then the schema release**: read each resolved store's `schema_version` and `Grep` the release in `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (*The schema release this plugin ships*). A store at it is not mentioned; one behind it is said in one line on every run, naming `/nk:upgrade`, and the run carries on; newer or absent follows that file's four answers.
2. **An overlay?** If `<store>/schema/files/` holds a definition, read `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md` before resolving one; otherwise every definition this run resolves is the shipped one.
3. **Infer before asking**: read what the conversation already states, and ask only what is still open.

Resolve the `resume.md`, `session.md` and `NOTES.md` definitions per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` before writing any of them - the user's overlay
wins over the shipped default.
A write that leaves a file with a numeric `budget:` at or past `budget_notice_pct` of it says so, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`.

**This command migrates nothing.** A file it would write that is behind its definition, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`, is not written. **`resume.md` or `session.md`
behind stops the whole save** - say so in those words, naming the file and `/nk:upgrade`, before
anything else in the report, and **say plainly not to `/clear` on the strength of that save**: the
session's record is what did not land. **Any other file behind** - `NOTES.md` for its stamp, a
register a promotion would write to - **is skipped and the save goes on**: name it and `/nk:upgrade`
on a warn line, and list a promotion it held back as not written.

Every repository fact it needs comes from git, per `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`.

## Five turns

**A save is five turns of parallel calls - the rules, the store, what the writes change, the writes,
the report.** Each turn sends every call it names in one message. The steps below say how each
answer is used and what each write holds; they add no calls of their own.

| Turn | Sends, all at once |
|---|---|
| **A - the rules** | `Read` every reference the steps cite: `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`, `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`, `${CLAUDE_PLUGIN_ROOT}/reference/item.md`, the `resume.md` and `session.md` definitions, `${CLAUDE_PLUGIN_ROOT}/skills/save/references/resume.md`, `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`, `${CLAUDE_PLUGIN_ROOT}/reference/drain.md`, `${CLAUDE_PLUGIN_ROOT}/reference/tags.md`, `${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md` and `${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md` |
| **B - the store and the targets** | resolving, per `resolve.md`, *In one turn*; in the working folder **`git branch --show-current` and `git rev-parse HEAD`, each its own call**; `Grep` `index.md` for every target - the ids given, or each item the conversation and the branch name, step 1; the memory listing, per `drain.md`, *Finding it* |
| **C - what the writes change** | for each target, `resume.md` - a failed `Read` is an item without one - and `session.md`'s `^## ` lines by `Grep` with line numbers - nothing of it where this session already read it; each memory file; for each candidate fact, the register its route names, with that scope's `areas/INDEX.md`; and the `NOTES.md` whose stamp step 7 refreshes |
| **D - the writes** | every write of steps 2 to 10, each its own call - `Write` for a file rewritten whole, `Edit` for one appended to |
| **E - the report** | the report, ending in the line it earns |

**Only a case the answers raise reads its rule, and spends a turn on it** - a mint, a question, a
file behind its definition, an overlay.

## The steps, in order

| # | Do | On failure |
|---|---|---|
| 1 | Take the store resolved above, then the targets inside it - every item the session worked on, or one minted if there is none (below). State the inference and name the store path; ask only if genuinely ambiguous, per `${CLAUDE_PLUGIN_ROOT}/reference/asking.md` | **abort** |
| 2 | Rewrite `resume.md` where the item has one, or where this save is its fifth saving session (`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/resume.md`, *When it starts*); otherwise skip it, and step 3 carries the position as its `next:` line. Rewrite it - the position, and the story below it, per `${CLAUDE_PLUGIN_ROOT}/skills/save/references/resume.md` (with the definition, `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/resume.md`, `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md` and `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md` for the branch) | **abort** |
| 3 | Append or extend today's `## session <date>` block in `session.md` - re-running the same day extends it. Record this session's id under the heading as `session: ${CLAUDE_SESSION_ID}`, one line per session the block covers, added once. With no `resume.md` after step 2, end the block with its `next:` line, per `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/session.md` | **abort** |
| 4 | Where the session changed what was asked, or showed a requirement the item states to be wrong, append the dated amendment to `requirements.md` - *scope changed* or *understanding corrected*, with `Invalidates` filled - per `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/requirements.md`. **Only on what the session shows** - the user changing the ask, or a finding that contradicts a stated requirement. The body is never rewritten | **warn** |
| 5 | Promotion check - route, scope, dedupe, classify, write every verdict that writes, then report what was written, per `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`. A credential goes in only where the user asked to keep it - to `secrets.md`, and the report names the entry, never the value | **abort** |
| 6 | Drain the harness memory store - read it, promote what survives admission, **clear every item whose content is in the store - promoted now, or already there and checked for its place; a one-off filed in a work item's record first; one already in a registered repository's files judged for the store, then cleared** - and report what could not be placed, per `${CLAUDE_PLUGIN_ROOT}/reference/drain.md` (promotion per `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`) | **warn** - name the store and what is still in it |
| 7 | Refresh `NOTES.md`'s verified stamp if the repo or environment moved, dated today - a stamp's date is the day it is written. The repo half is the repo-facts rule's *the commit*; unreadable writes it *unverified* | **warn** |
| 8 | Settle the item's tags - `--tag`, plus what the session named, per `${CLAUDE_PLUGIN_ROOT}/reference/tags.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md`) | **warn** |
| 9 | Regenerate the store's `index.md` per `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` and `${CLAUDE_PLUGIN_ROOT}/reference/index-writing.md` - a save that changed no id, related id, title, project, parent or tag leaves it byte-identical | **abort** |
| 10 | Where the session says the work is done, write the closing block - one dated heading and one line of reason, shape fixed in `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/session.md`, *Closing, and staying open*. **Only on what the user said**, never on your own reading of the work | **warn** |
| 11 | Does the repo you are standing in resolve to a project - by its read line or the registry? If neither, say so and offer to initialise it; if its read line resolves it and the registry has lost the folder, say that in one line. Both per `${CLAUDE_PLUGIN_ROOT}/skills/save/references/unregistered.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md` and `${CLAUDE_PLUGIN_ROOT}/reference/store/repo-facts.md`) | **warn** |

**Several targets: steps 2, 3, 4, 8 and 10 run per item, in id order; steps 5, 6, 7, 9 and 11 run
once.** What describes an item - its resume, its session block, its amendment, its tags, its closing -
is per item; what describes the session or the store is not, and running it per item would promote
one fact several times. An abort step failing on one item stops the save there: the report names the items
written and the items not written.

> **A save writes no projection.** A repository's `CLAUDE.local.md` holds one line that reads the
> notes, and that line does not change when the notes do.
>
> Step 11 runs last and never aborts. It is about where the user is
> standing, not about what they asked to save.

## Step 1 in full - resolving, or minting

What a mint produces is `${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md` - the id, the counter,
the slug, the folder, `requirements.md`, the project. Follow it; this command decides only
whether to mint, never what a bundle looks like. `/nk:work` mints against the same file, so the
two produce identical bundles.

1. **`[id...]` given?** Resolve each per `${CLAUDE_PLUGIN_ROOT}/reference/item.md`. Each that resolves
   is a target - **except a run item** (tagged `run-<command>`), which is the run's own record and is
   never saved into: say so, name the run's command and `--apply`, and leave it out. If one does not, ask whether to mint it as a new item, naming any close
   match (`0012` for `12`) - an id that does not resolve is never minted unasked; `/nk:work --id <id>` mints
   it explicitly.
2. **No `[id]`?** Infer **every item the session worked on** - wrote to, decided something for, or
   moved forward. **An item only loaded, listed or mentioned is not one**: a summary that names every
   item works on none of them. State the list with the store path; ask only about an item that was
   loaded with nothing decided for it. Resolve each per `${CLAUDE_PLUGIN_ROOT}/reference/item.md` - the inference, and the
   directory fallback when there is no `index.md`, in which case regenerate the index at step 9 as
   usual. The branch is one input to the inference, and the conversation leads: the repo-facts
   rule's *the branch*. A detached `HEAD` names no branch, and a sha is never an item. **Branch
   unknown** - git unavailable and the fallback empty - leaves the inference to the conversation and
   the item; ask rather than guess.
3. **No match at all → mint.** With targets resolved, work outside every target is never minted -
   it goes to the report's *Not saved*. **Anything short of confident → ask**, per `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`. Naming the item costs one
   line; the alternative costs a duplicate.

`--project <name>` applies to a mint only. It names the project of a bundle being created; it
never retargets one that already exists. `--tag` applies to every target. **`--dry-run`** reports
each target it resolved or would mint, and every file it would touch, and writes nothing.

## Not saved

**Work that belongs to no target is routed by tense.** Knowledge is promoted at step 5, as always.
The rest is named in the report under **Not saved**, one line each - what it was, and where it would
go:

> Not saved: the README's install section rewritten - finished, git holds it
> Not saved: the flaky login test, investigated halfway - in flight, `/nk:work` would track it

Nothing is written for it. The section is there so that work leaves the session visibly, and the
user can make an item of it; with nothing outside the targets, it is left out.

The steps marked **abort** are the durable invariant. If any fails, **report failure and name what was and was not
written.** Never report success on a partial run.

## Asking

**Every question this skill asks follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`** - `nk:save
needs:` and the open questions.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:save`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

## End by confirming it is safe to leave

Name the command that gets them back. `/nk:load` needs no argument - it infers from the branch
and the latest saved session, both of which survive the clear.

> Saved. Safe to `/clear` - resume with `/nk:load`.

This line is a claim about the session, and it is earned rather than printed. It says what the
conversation held is written, so it may be written **only where every step marked *abort* completed**.
Where one did not, the report names what was not written, in place of this line - never alongside it.

A step marked *warn* never withholds it - a drain that could not run, a tag not settled, a
closing block not written, a file skipped as behind its definition, an amendment not written, an unregistered repo, a folder the registry has lost, or a promotion that found nothing to admit. Each
goes on its own line above the claim, and the claim still stands. The drain is not in that claim.
What it left behind goes on its own line above:

> Memory not drained: `<path>` could not be read. It is still there; the next save retries.
