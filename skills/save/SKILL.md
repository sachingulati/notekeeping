---
name: save
description: Checkpoint the current session into the work item's record - the handover, what was learned, the harness memory - so it survives /clear. Use when the user asks to save, checkpoint, wrap up, or keep what this session found.
argument-hint: "[id] [--project <name>] [--tag <name>] [--dry-run] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

The checkpoint. Idempotent and safe to run many times per session - that is the normal usage, not
the exception.

Before anything else, in order:
1. **`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
2. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`.
3. **An overlay?** If `<store>/schema/skills/save/` exists, follow `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`: a `SKILL.md` there replaces the rest of this file, and a file under its `references/` replaces the shipped reference of that name wherever this skill cites it.
4. **Infer before asking**: read what the conversation already states, and ask only what is still open.

Resolve the `resume.md`, `session.md` and `NOTES.md` definitions per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` before writing any of them - the user's overlay
wins over the shipped default.
A write that leaves a file with a numeric `budget:` at or past `budget_notice_pct` of it says so, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/budget-notice.md`.

Every write in this command is migration-on-write, per
`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`. Where the resolved definition's `## Migration`
declares a step on write, convert the file and stamp it in the same write, and name the
conversion in the report - `${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`). Where it declares
upgrade only, do not convert and do not write that file: report it as outstanding and name
`/nk:upgrade`. Never walk the store for files you were not already writing, and never move
`schema_version`.

**Where an `upgrade only` step reaches a file an earlier step writes, the whole save stops, and the
report says so in those words** - that step aborts, and every later step is about work that did not
happen. Name it before anything else in the report, and **say plainly not to `/clear` on the strength
of that save**. Under `--oneline` this is the `failed` status
(`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`).

**This command runs no shell and no git.** Every repository fact it needs is a file it reads.

## The steps, in order

| # | Do | On failure |
|---|---|---|
| 1 | Take the store resolved above, then the target inside it - minting one if there is none (below). State the inference and name the store path; ask only if genuinely ambiguous, per `${CLAUDE_PLUGIN_ROOT}/reference/asking.md` | **abort** |
| 2 | Rewrite `resume.md` - the position, and the story below it, per `${CLAUDE_PLUGIN_ROOT}/skills/save/references/resume.md` (with the definition, `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/resume.md`, `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md` and `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md` for the branch) | **abort** |
| 3 | Append or extend today's `## session <date>` block in `session.md` - re-running the same day extends it. Record this session's id under the heading as `session: ${CLAUDE_SESSION_ID}`, one line per session the block covers, added once; nothing reads it | **abort** |
| 4 | Promotion check - route, scope, dedupe, classify, write every verdict that writes, then report what was written, per `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md` | **abort** |
| 5 | Drain the harness memory store - read it, promote what survives admission, **clear what was promoted**, and report what could not be placed, per `${CLAUDE_PLUGIN_ROOT}/reference/drain.md` (promotion per `${CLAUDE_PLUGIN_ROOT}/reference/promotion.md`) | **warn** - name the store and what is still in it |
| 6 | Refresh `NOTES.md` - the active pointer, and the verified stamp if the repo or environment moved, dated today - a stamp's date is the day it is written. The repo half is read, never run: `.git/HEAD`, then the ref file it names or `packed-refs`, per `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`; unreadable leaves the repo half unstamped | **abort** |
| 7 | Settle the item's tags - `--tag`, plus what the session named, per `${CLAUDE_PLUGIN_ROOT}/reference/tags.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md`) | **warn** |
| 8 | Regenerate the store's `index.md` per `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` - a save that changed no id, related id, title, project, parent or tag leaves it byte-identical | **abort** |
| 9 | Where the session says the work is done, write the closing block - one dated heading and one line of reason, shape fixed in `${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/session.md`, *Closing, and staying open*. **Only on what the user said**, never on your own reading of the work | **warn** |
| 10 | Does the repo you are standing in resolve to a project - by its read line or the registry? If neither, say so and offer to initialise it; if its read line resolves it and the registry has lost the folder, say that in one line. Both per `${CLAUDE_PLUGIN_ROOT}/skills/save/references/unregistered.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`, `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md` and `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`) | **warn** |

> **A save writes no projection.** A repository's `CLAUDE.local.md` holds one line that reads the
> notes, and that line does not change when the notes do.
>
> Step 10 is last on purpose, and it is never a reason to abort. It is about where the user is
> standing, not about what they asked to save.

## Step 1 in full - resolving, or minting

What a mint produces is `${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md` - the id, the counter,
the slug, the folder, `requirements.md`, the project. Follow it; this command decides only
whether to mint, never what a bundle looks like. `/nk:work` mints against the same file, so the
two produce identical bundles.

1. **`[id]` given?** Resolve it per `${CLAUDE_PLUGIN_ROOT}/reference/item.md`. If it resolves, that
   is the target. If it does not, ask whether to mint it as a new item, naming any close
   match (`0012` for `12`) - an id that does not resolve is never minted unasked. Under `--oneline`
   this is a refusal naming the id and the close matches; `/nk:work --id <id>` mints it explicitly.
2. **No `[id]`?** Resolve the target per `${CLAUDE_PLUGIN_ROOT}/reference/item.md` - the inference, and the
   directory fallback when there is no `index.md`, in which case regenerate the index at step 8 as
   usual. The branch is one input to the inference, and the conversation leads: read
   it from `.git/HEAD`'s `ref:` line per `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md` - in a
   worktree, through `gitdir:`. A detached `HEAD` names no branch, and a sha is never an item.
3. **No match at all → mint.** **Anything short of confident → ask**, per `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`. Naming the item costs one
   line; the alternative costs a duplicate.

`--project <name>` applies to a mint only. It names the project of a bundle being created; it
never retargets one that already exists. **`--dry-run`** reports the target it resolved or would
mint, and every file it would touch, and writes nothing.

The steps marked **abort** are the durable invariant. If any fails, **report failure and name what was and was not
written.** Never report success on a partial run. Under `--oneline` that failure is the `failed`
status, with the detail naming what was and was not written
(`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`).

## Asking

**Every question this skill asks follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`** - `nk:save
needs:` and the open questions - with `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` when
nobody can answer.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:save`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.

## End by confirming it is safe to leave

Name the command that gets them back. `/nk:load` needs no argument - it infers from the branch
and the files touched, both of which survive the clear.

> Saved. Safe to `/clear` - resume with `/nk:load`.

This line is a claim about the session, and it is earned rather than printed. It says what the
conversation held is written, so it may be written **only where every step marked *abort* completed**.
Where one did not, the report names what was not written, in place of this line - never alongside it.

A step marked *warn* never withholds it - a drain that could not run, a tag not settled, a
closing block not written, an unregistered repo, a folder the registry has lost, or a promotion that found nothing to admit. Each
goes on its own line above the claim, and the claim still stands. The drain is not in that claim.
What it left behind goes on its own line above:

> Memory not drained: `<path>` could not be read. It is still there; the next save retries.
