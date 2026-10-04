---
doc:   index-shape
title: The shape of index.md, and the one rule that decides what earns a column
---

# The resolver's shape

> **The resolver carries what a command resolves *by*, plus what is needed to choose between
> candidates without opening a file. Nothing else.**

A user-defined field that nothing resolves by earns no column; grep finds it.

## The columns

**Seven, in this order. Every one is derived** - six from a `requirements.md` frontmatter field,
which stays authoritative, and `folder` from the path on disk.

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
| 0003 | TKT-482 | Scrollbar and filter usability | work/2026-08/0003-scrollbar-and-filter | web-client | | usability, a11y |
| TKT-517 | | Counter rollup | work/2026-08/TKT-517-counter-rollup | repo-a, repo-b | TKT-500 | |
```

**The three relation columns - `ids`, `parent`, `tags` - are each declared in the schema and
accepted by `/nk:work`.** Indexing them means **one lookup serves all three**; without it a relation
can be set and never walked.

## Recency is derived

**`session.md` is append-only with dated session blocks, so its newest block *is* last activity** -
found by `Grep` `^## session ` with line numbers and take the last match, which is a heading match and never a read of the file.
Nothing maintains that, and it cannot drift, because it is not a field - it is the record.

**Fall back to the `work/<bucket>/` bucket** when there is no `session.md`. A just-minted item has
`requirements.md` and nothing else, so there is no session block to read; the bucket is the period of
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

**The list columns are `projects`, `tags` and `ids`, and a bare substring match on any of them is
wrong.** `auth` must not match `oauth`, and `a11y` must not match `a11y-audit`. The cell is
comma-and-space separated, so a token is bounded by `|`, `,` or the cell edge - match on that
boundary, and when in doubt read the row and decide there rather than tightening the regex.

**Narrow with grep, then interpret the handful of rows that come back.** Positional regex over a
pipe-delimited table is brittle, and this is a prompt rather than a parser: the grep buys cheapness
and a stable surface, not correctness.

**Regenerating it** - a row's rendering, the overwrite check, what is not a column, and when it changes - is the index-writing rule.
