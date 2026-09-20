---
doc:   index-shape
title: The shape of index.md, and the one rule that decides what earns a column
---

# The resolver's shape

> **The resolver carries what a command resolves *by*, plus what is needed to choose between
> candidates without opening a file. Nothing else.**

**Two clauses, because one does not justify every column.** Nothing resolves *by* a project, but a
candidate list is unreadable without one. The two clauses together exclude `status`, `type` and
`created` at once, and they answer the overlay case by principle rather than by enumeration: a
user-defined field that nothing resolves by earns no column, and grep remains the fallback it
always was.

**This file exists because six commands touch this one file.** `/nk:index` rebuilds it for repair,
`/nk:save` and `/nk:work` regenerate it as a matter of course, `/nk:adopt` writes it where an
adoption produced work items, `/nk:upgrade` regenerates it rather than migrating it, and `/nk:load`
resolves against it. Two writers that state the shape separately drift - one rendering an empty
project list as an empty cell and the other as `[]`. One shape, stated once, cited by all six.

## The columns

**Seven, in this order. Every one is derived from a `requirements.md` frontmatter field**, which
stays authoritative.

| Column | From | Why it earns a place |
|---|---|---|
| `id` | `id` | the primary resolution key |
| `ids` | `ids` | **resolves.** A secondary tracker id is how a colleague names the work |
| `title` | `title` | choosing between candidates |
| `folder` | the path on disk | where to go once you have chosen |
| `projects` | `project` - **note the name change** | choosing between candidates |
| `parent` | `parent` | **resolves.** The roll-up walks down it |
| `tags` | `tags` | **resolves.** Naming a tag returns every item carrying it |

```markdown
| id | ids | title | folder | projects | parent | tags |
|---|---|---|---|---|---|---|
| 0003 | TKT-482 | Scrollbar and filter usability | 2026-08/0003-scrollbar-and-filter | web-client | | usability, a11y |
| TKT-517 | | Counter rollup | 2026-08/TKT-517-counter-rollup | repo-a, repo-b | TKT-500 | |
```

**The three relation columns - `ids`, `parent`, `tags` - are each declared in the schema and
accepted by `/nk:work`.** Indexing them means **one lookup serves all three**; without it a relation
can be set and never walked.

## Writing a row

**The column is `projects`, plural, and holds the frontmatter list verbatim.** A single-valued
column silently drops the second repository of every multi-repo item.

**The frontmatter key it reads is `project`, singular, with a list value.** The two names differ on
purpose and must not be swapped: every command that reports git state reads `project:`, so a bundle
written with `projects:` breaks them silently.

**Render an empty value as an empty cell - never as `[]`, `none` or `-`.** Two commands regenerate
this file routinely; if they render the same value differently the file churns on every save and
produces a diff that means nothing - the drift named at the top of this file.

**Before overwriting, check for content that is not derivable from frontmatter.** If there is any,
**stop and report it** rather than destroying it. Regenerating from frontmatter deletes anything
that exists only here, which is why narrative must never live in this file.

## What is not here

| Not a column | Why |
|---|---|
| `status` | Not in frontmatter. A work item holds no workflow state, and a cached live status carries an authoritative look while going stale the moment somebody *else* acts |
| `updated` | **Recency is derived, not stored** - below. Stored, it makes the file churn on every save to move one date |
| `created`, `type` | Neither resolves, and neither is needed to choose between candidates |

## Recency is derived

**`session.md` is append-only with dated session blocks, so its newest block *is* last activity** -
`grep -n '^## session ' | tail -1`, which is a heading match and never a read of the file.
Nothing maintains that, and it cannot drift, because it is not a field - it is the record.

**Fall back to the `work/<YYYY-MM>/` bucket** when there is no `session.md`. A just-minted item has
`requirements.md` and nothing else, so there is no session block to read; the bucket is the month of
first work, and for a fresh item it is the newest thing anyway.

**Order candidates newest first.** That costs one cheap read per *candidate*, not per item, and the
candidate set is small by construction - a query returning thirty candidates is a query problem, not
a sorting problem. **A tag needs none of it**: the bucket path is already chronological, and the
set a tag returns walks **oldest first**.

## It is grepped, never read

**Never load this file whole.** Doing so puts its full size into every resolution and scales
linearly with the store.

| Query | Grep |
|---|---|
| an exact id | **anchored on column 1** - `^\| <id> \|` - which is deterministic |
| a tag, or a project | the cell holds a **list**, so match the whole token between delimiters - never a bare substring |
| anything else | unanchored, then read only the rows it returned |

**The list columns are `projects` and `tags`, and a bare substring match on either is wrong.**
`auth` must not match `oauth`, and `a11y` must not match `a11y-audit`. The cell is
comma-and-space separated, so a token is bounded by `|`, `,` or the cell edge - match on that
boundary, and when in doubt read the row and decide there rather than tightening the regex.

**Narrow with grep, then interpret the handful of rows that come back.** Positional regex over a
pipe-delimited table is brittle, and this is a prompt rather than a parser: the grep buys cheapness
and a stable surface, not correctness.

## When it changes

**Only at mint, or when an item's identity, relations or tags change.** Every other save leaves this
file byte-identical, so the regeneration step is a genuine no-op rather than a rewrite that moves one
date. A save that reports this file as changed when none of `id`, `title`, `projects`, `parent` or
`tags` moved is a defect in the writer, not a property of the store.

**Tags are the one column a routine save can legitimately move**, because `/nk:save` both accepts
`--tag` and picks tags up from the session. That is a real change to identity, not churn - name it
when it happens.
