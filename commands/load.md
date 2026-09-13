---
description: Resume. Reads only, never writes - restores where you were from the store, git and the tracker.
argument-hint: "[query] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Bash(git status:*), Bash(git branch:*), Bash(git log:*), Bash(git diff:*), Bash(git stash list:*)
---

Resume where you left off. **This command reads. It never writes anything, anywhere.**

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: never ask - refuse naming the argument
that would satisfy it - and end with the outcome line.

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
says so plainly. **Newest is derived** - the newest dated block in each candidate's `dev.md`,
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
- `dev.md`: the handover block, and the latest session block.
- `plan.md` next steps and `test.md` status, if they exist.

## 3. Tracker, if an adapter is enabled

Fetch the issue **with its comments and sub-tasks** - the decisive context routinely sits there
rather than in the structured fields. Fetch every id in `ids:`.

## 4. Git state

For each project in the item's `project:` list **and their declared dependencies**: whether the
branch exists, ahead/behind counts, dirty files excluding configured local-only ones, stashes
mentioning the id, the last three commits, and a diff stat against the default branch.

## 5. Parent roll-up

**Find children from the index's `parent` column** - grep it for this item's `id`. That is the
column's second job, and it is why the roll-up no longer needs a tree-wide scan to discover what it
is rolling up.

If the item has children, one screen: each child's handover block.

## 6. MR and pipeline state

Only when one is already known. **No speculative lookups.**

## Output

**Under `--caller`: one line, and nothing else.** Follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` - the outcome line **is** the output.
Compress what matters into its detail: the item, the project, branch and cleanliness, which
artifacts exist, and the single most important discrepancy if there is one.
**The screen below is the human path and does not apply.**

```
nk: load ok — TKT-482 (repo-a); branch main, clean; requirements only, no plan/test; 1 discrepancy
```

**Otherwise - one screen:**

```
where it stands -> next actions -> blockers -> code state -> discrepancies
```

**The discrepancies section is the point.** The notes, the tracker and git each hold a version of the
truth and they drift. Naming the drift is worth more than any one of them: say plainly where the
handover disagrees with the branch, where the tracker disagrees with the notes, and which you think
is right.
