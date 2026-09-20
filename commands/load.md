---
description: Resume. Reads only, never writes - restores where you were from the store, git and the tracker.
argument-hint: "[query] [--full] [--quick] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Bash(git status:*), Bash(git branch:*), Bash(git log:*), Bash(git diff:*), Bash(git stash list:*)
---

Resume where you left off. **This command reads. It never writes anything, anywhere.**

**`--caller <name>`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

## 1. Resolve the store, then the query

**Resolve the store first, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and search
only inside it.** Never infer a store from the filesystem: a directory that looks like notes is not
this store, and resolving against the wrong one produces a confident, entirely fictional answer.

**Then resolve the query against `index.md`, per
`${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md`** - which is the resolver, carries every column
this chain matches on, and is **grepped, never read whole.**

| # | Match `$ARGUMENTS` against | How |
|---|---|---|
| 1 | `id`, then `ids` | **anchored** grep - an exact key, deterministically |
| 2 | `tags` | whole-token match in the list cell. Returns **every item carrying it**, oldest first |
| 3 | `parent` | exact. Returns **the children** of that id |
| 4 | `id`, `title`, `folder` as a **substring** | unanchored grep; read only the rows returned |
| 5 | **the file bodies**, across the store | only now, and say that you fell through to it |

**Stop at the first step that matches.** Each is cheaper and more certain than the one below it,
and **falling through silently is the failure this table prevents**: a secondary tracker id that
reaches step 5 resolves by luck, on an item whose `ids:` list **§3 below** will then fetch from the
tracker by name. If a step matches, say which one - a resolution that cannot name its step is a
resolution nobody can check.

**A set is not an ambiguity, and this is the distinction that makes steps 2 and 3 worth having.**
Several rows from step 4 are *candidates* - list them and let the user pick. Several rows from
step 2 or 3 are **the answer**: a tag names a set and a parent has children, so load the set
rather than asking which member was meant. Say which of the two happened.

**What a set renders is not what one item renders.** Each member gets its handover block and its
`summary.md` if it has one - **never the full bundle for each**, because a thread of eight items
would bury the screen. A tag's set walks **oldest first**, and needs no recency read at all: the
`work/<YYYY-MM>/` bucket is already chronological. Children of a parent follow §5.

**A query can match at more than one step, and the earlier step wins - say what you did not take.**
An id and a tag can be spelled the same, and step 1 will silently win. Naming the interpretation
you passed over costs one line and is the only way the collision is ever visible: *"resolved as an
id; `<query>` is also a tag on four items."*

Otherwise: one hit loads it; several candidates are listed to pick from, **newest first**; none
says so plainly. **Newest is derived** - the newest dated block in each candidate's `session.md`,
falling back to its `work/<YYYY-MM>/` bucket. One cheap read per candidate, never per item.

A bare invocation infers from the current branch, recently touched files, and what this session has
discussed - **all of it scoped to the configured store**, and **resolved against `index.md` rather
than against your memory of the session.** State the inference you made, and name the store path
you used, so a wrong root is visible immediately rather than after acting on it.

Terms that appear only in file bodies still resolve by grep in well under a second at no token cost.
Step 5 is a legitimate destination, not a failure - it is last because it is the only one that
cannot say *why* it matched.

## 2. Read the bundle

- `requirements.md` in **`active`** mode - the amendment chain resolved to current.
- **`resume.md` whole.** It is the synthesis - position, the story, why each decision was made, and
  what has already been ruled out - and it is what makes a cold session continuous rather than
  merely informed.
- **`session.md`, how much depending on the depth** - see *Depth* below. On `--quick`, the latest
  block alone, **`grep -n '^## session ' | tail -1`**, read from there to the next `## ` heading or
  the end. On `--full`, the file. Every block names its kind, so the part wanted is addressable
  without the ones before it.
- `plan.md` next steps and `test.md` status, if they exist.
- **`instructions.md` whole, if it exists** - what this item requires you to *do* while it is in
  flight. Its budget is small enough that bounding the read would cost more than it saves.

**This is the only delivery that file has**, which is why it is read whole and read first among the
optional ones: the project's and the workspace's instructions arrive by projection, and re-reading
them here would pay twice for the same lines. **Say in the report that the item carries instructions
and how many** - an instruction that loaded silently is indistinguishable, from the outside, from
one that did not load at all.

**Check that the projection actually delivered them; never assume it did.** A projection can be
missing, stale, or stripped of its block - `/nk:doctor` carries the finding and `/nk:project <name>`
rebuilds it - so *already in context* is a claim about a file that may not exist. **Look for the
literal `## Standing instructions` heading** in `<repo>/CLAUDE.local.md` and in the workspace root's,
per `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, which is the same detection `/nk:doctor` uses
and costs one grep per scope.

| What you find | Do |
|---|---|
| the heading is there | say the scope's instructions arrived by projection, and do not re-read them |
| the block is missing, or has no such heading, **and that scope's `instructions.md` has content** | **read it and render it here**, say that it came from the store rather than the projection, and name `/nk:project <name>` or `/nk:doctor --fix` |
| that scope has no `instructions.md`, or it is empty | nothing to say |

**Reporting them as loaded when nothing loaded them is the failure this prevents**, and it is worse
than silence: the user is told a standing instruction is in force while the session cannot see it.

## Depth - what is read of `session.md`

| | Reads | Then a save can |
|---|---|---|
| **`--quick`** | `resume.md` + the latest session block | only carry `resume.md` forward, which is the one path that can drift |
| **`--full`** | `resume.md` + all of `session.md` | **re-derive `resume.md` from the record**, at no extra cost, the history already being in context |

**`--quick` is the default**, and `load_depth` in the store's config changes it
(`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`). A flag beats the setting.

**Depth governs `session.md` and nothing else.** `requirements.md`, `plan.md`, `test.md` and
`instructions.md` are bounded and cumulative, and both depths read them identically. A `--quick`
that also read less of those would be answering a different question.

**Never read `session.md` twice in one session.** A second read is not recognised as a repeat: it
appends a second copy at full price and both are re-sent on every turn after it, where merely having
read it once is charged at cache rates. So a save later in this session uses what is already here
and reads only what is missing.

## 3. Tracker

**Only where this session already reaches the tracker** - an MCP server the user has connected, or a
CLI they run. The fetch is theirs, not this plugin's: it configures no tracker and holds no
credentials, so where there is no such tool the step is skipped and the bundle is the source.

Where there is one, fetch the issue **with its comments and sub-tasks** - the decisive context
routinely sits there rather than in the structured fields. Fetch every id in `ids:`.

## 4. Git state

For each project in the item's `project:` list **and their declared dependencies**: whether the
branch exists, ahead/behind counts, **dirty files excluding the paths `ignore_dirty` names**
(`${CLAUDE_PLUGIN_ROOT}/reference/config-defaults.md`; unset means exclude nothing), stashes
mentioning the id, a diff stat against the default branch, and **the commits, bounded by the work
rather than by a number**:

| | Read |
|---|---|
| the branch is not the default branch | **`git log <default>..HEAD`** - every commit on it. The branch is the item, so the branch is the bound |
| the branch **is** the default branch | **`git log --since=<date of the newest block in `session.md`>`** - what has happened since the last checkpoint |

**Neither bound is a count.** The diff stat already says *what* changed, cumulatively; commits are
read for **sequence and intent**, which the stat cannot carry - and a fixed number of them keeps the
end of the story and drops its beginning.

## 5. Parent roll-up

**Find children from the index's `parent` column** - grep it for this item's `id`. That is the
column's second job, and it is what spares the roll-up a tree-wide scan.

If the item has children, one screen: **each child's `## Where things stand` region only**, bounded
by the heading. **Never a child's whole `resume.md`** - that file is a full summary now, and a parent
with five children would pull five of them to render one screen.

## 6. MR and pipeline state

Only when one is already known, and only through access this session already has. **No speculative
lookups**, and nothing is fetched on this plugin's behalf.

## Output

**Under `--caller`: one line, and nothing else.** Follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` - the outcome line **is** the output.
Compress what matters into its detail: the item, the project, branch and cleanliness, which
artifacts exist, and the single most important discrepancy if there is one.
**The screen below is the human path and does not apply.**

```
nk: load ok — TKT-482 (repo-a); branch main, clean; requirements only, no plan/test; 1 discrepancy
```

**Otherwise - one screen, in this order, filled from the run and never copied from here.** It is
rendered from `resume.md`, which already holds the story; this is the shape it is reported in, not a
second act of synthesis:

```
<id> - <title>  (<project>)

where it stands   <where the handover left it>
next actions      <the next thing, and the one after>
blockers          <what is in the way> | none
code state        branch <branch>, <clean> | <n dirty>; <ahead/behind> ; <stashes>
discrepancies     <source> disagrees with <source>: <what>, and <which you think is right>
                  none found
```

**All five labels are printed every run.** A section with nothing in it carries `none`, because
*no blockers* and *blockers not checked* are different answers and the reader cannot tell them
apart from a missing line.

**The discrepancies section is the point.** The notes, the tracker and git each hold a version of the
truth and they drift. Naming the drift is worth more than any one of them: say plainly where the
handover disagrees with the branch, where the tracker disagrees with the notes, and which you think
is right.
