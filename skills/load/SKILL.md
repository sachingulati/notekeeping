---
name: load
description: "Resume a work item - read its record and say where things stand and what is next. Reads only. Use when the user asks to resume or pick up earlier work, or what they were last doing on it."
argument-hint: "[query] [--full] [--quick] [--oneline]"
allowed-tools: Read, Glob, Grep
---

Resume where you left off. **This command reads. It never writes anything, anywhere.**

Before anything else, in order:
1. **`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
2. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`.
3. **An overlay?** If `<store>/schema/skills/load/` exists, follow `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`: a `SKILL.md` there replaces the rest of this file, and a file under its `references/` replaces the shipped reference of that name wherever this skill cites it.
4. **Infer before asking**: read what the conversation already states, and ask only what is still open.

## 1. Resolve the store, then the query

**Search only inside the resolved store.** Never infer a store from the filesystem: a directory that looks like notes is not
this store, and resolving against the wrong one produces a confident, entirely fictional answer.

Then resolve the query per `${CLAUDE_PLUGIN_ROOT}/reference/item.md`, against `index.md` per
`${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` - which is the resolver, carries every column the match
uses, and is grepped, never read whole. One invocation loads one item; where several match,
ask which (`## Asking`). Name the column that
matched; a resolution that cannot say how it matched is one nobody can check.

## 2. Read the bundle

- `requirements.md` in `active` mode - the amendment chain resolved to current.
- **`resume.md` whole.** It is the synthesis - position, the story, why each decision was made, and
  what has already been ruled out - and it is what makes a cold session continuous rather than
  merely informed.
- **`session.md`, how much depending on the depth** - see *Depth* below. On `--quick`, the latest
  block alone - `Grep` `^## session ` with line numbers and take the last match - read from there to the next `## ` heading or
  the end. On `--full`, the file. Every block names its kind, so the part wanted is addressable
  without the ones before it.
- **`instructions.md` whole, if it exists** - what this item requires you to *do* while it is in
  flight. Its budget is small enough that bounding the read would cost more than it saves.
- `plan.md` next steps and `test.md` status, if they exist.

The item's `instructions.md` has no other delivery, which is why it is read whole and read first among the
optional ones: the project's, the workspace's and global's instructions arrive through the read line, and re-reading
them here would pay twice for the same lines. Say in the report that the item carries instructions
and how many - an instruction that loaded silently is indistinguishable, from the outside, from
one that did not load at all.

**Check that the read line actually delivered them; never assume it did.** A read line can be
missing or stripped - `/nk:doctor` carries the finding and `/nk:doctor --fix` writes it back - so
*already in context* is a claim about a line that may not exist. Ask of each target whether it holds
the read line - `<repo>/CLAUDE.local.md`, the workspace root's, and `~/.claude/rules/notekeeping.md` -
per `${CLAUDE_PLUGIN_ROOT}/reference/projections.md` (with `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`), which is the same
detection `/nk:doctor` uses and costs one grep per scope. Global's rule file reaches every
session; a repo's line reaches only a session whose working directory is that
repo's root or a descendant of it. Outside that, treat the line as unread even if the grep finds
it - the file exists, but this session never loaded it.

| What you find | Do |
|---|---|
| the line is there, and this session is inside the scope's reach | say the scope's instructions arrived through the read line, and do not re-read them |
| the line is missing, or this session is outside the scope's reach, and that scope's `instructions.md` has content | read it and render it here, say that it came from the store rather than the read line, and name `/nk:doctor --fix` |
| that scope has no `instructions.md`, or it is empty | nothing to say |

Reporting them as loaded when nothing loaded them is the failure this prevents, and it is worse
than silence: the user is told a standing instruction is in force while the session cannot see it.

## Depth - what is read of `session.md`

| | Reads | Then a save can |
|---|---|---|
| **`--quick`** | `resume.md` + the latest session block | only carry `resume.md` forward, which is the one path that can drift |
| **`--full`** | `resume.md` + all of `session.md` | re-derive `resume.md` from the record, at no extra cost, the history already being in context |

`--quick` is the default, and `load_depth` in the store's config changes it
(`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`). A flag beats the setting.

Depth governs `session.md` and nothing else. `requirements.md`, `plan.md`, `test.md` and
`instructions.md` are bounded and cumulative, and both depths read them identically. A `--quick`
that also read less of those would be answering a different question.

**Never read `session.md` twice in one session.** A second read is not recognised as a repeat: it
appends a second copy at full price and both are re-sent on every turn after it, where merely having
read it once is charged at cache rates. So a save later in this session uses what is already here
and reads only what is missing.

## 3. Tracker

Fetch every id in `ids:`, per `${CLAUDE_PLUGIN_ROOT}/reference/tracker.md` - only through access this session
already has, and a tracker it cannot reach is named, never guessed.

## 4. The branch

**This skill runs no shell and no git.** For each project in the item's `project:` list and their
declared dependencies, read the branch of its repository, found per
`${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`, *Which repository*: `.git/HEAD`'s `ref:`
line - in a worktree, through `.git`'s `gitdir:`. The branch
exists when its ref file is there, or `packed-refs` holds a line for it. A detached `HEAD` names no
branch: say so. **Nothing else of git is read** - no stashes, no diff, no commits.

## 5. Parent roll-up

Find children from the index's `parent` column - grep it for this item's `id`. That is the
column's second job, and it is what spares the roll-up a tree-wide scan.

If the item has children, one screen: each child's `## Where things stand` region only, bounded
by the heading. **Never a child's whole `resume.md`** - that file is a full summary, and a parent
with five children would pull five of them to render one screen.

## 6. MR and pipeline state

Only when one is already known, and only through access this session already has. **No speculative
lookups**, and nothing is fetched on this plugin's behalf.

## Output

Under `--oneline`: one line, and nothing else. Follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` - the outcome line is the output.
Compress what matters into its detail: the item, the project, the branch, which
artifacts exist, and the single most important discrepancy if there is one.
The screen below is the human path and does not apply.

```
nk: load ok — TKT-482 (repo-a); branch main; requirements only, no plan/test; 1 discrepancy
```

Otherwise - one screen, in this order, filled from the run and never copied from here. It is
rendered from `resume.md`, which already holds the story; this is the shape it is reported in, not a
second act of synthesis:

```
<id> - <title>  (<project>)
resolved          <store path>; matched by <id | ids | title | tags | folder>
where it stands   <where the handover left it>
next actions      <the next thing, and the one after>
blockers          <what is in the way> | none
code state        branch <branch> | detached | no repository
instructions      <n> arrived by the read line | <n> read from the store | none
children          <child id - one line each> | none
discrepancies     <source> disagrees with <source>: <what>, and <which you think is right>
                  none found
```

Every label is printed every run. A section with nothing in it carries `none`, because
*no blockers* and *blockers not checked* are different answers and the reader cannot tell them
apart from a missing line.

The discrepancies section is the point. The notes, the tracker and the branch each hold a version
of the truth and they drift. Naming the drift is worth more than any one of them: say plainly where
the handover disagrees with the branch, where the tracker disagrees with the notes, and which you
think is right.

## Asking

**Every question this skill asks follows `${CLAUDE_PLUGIN_ROOT}/reference/asking.md`** - `nk:load
needs:` and the open questions - with `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` when nobody can
answer. **Asking is the one thing a load may add**: it still writes nothing.

**A plain-text answer is applied by this skill, never by you.** When a question this skill asked is
answered in words rather than a pick - *yes* included - your next action is the `Skill` call:
`nk:load`, with the arguments this run was given, unchanged. Nothing before it - no read, no write,
no reply. That run, not you, carries out the answer. This holds while this file's text is still in
the conversation, and when the command was typed.
