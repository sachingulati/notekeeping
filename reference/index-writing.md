---
doc:   index-writing
title: Regenerating index.md - writing a row, and when it changes
---

# Writing the resolver

**The columns, and how the file is read, are the index-shape rule.** This is how a writer regenerates it.

## Writing a row

**The column is `projects`, plural, and holds the frontmatter list verbatim.** A single-valued
column silently drops the second repository of every multi-repo item.

**The frontmatter key it reads is `project`, singular, with a list value.** The two names differ on
purpose and must not be swapped: every command that reads a project's repository reads `project:`,
so a bundle written with `projects:` breaks them silently.

**Render an empty value as an empty cell - never as `[]`, `none` or `-`.** Two commands regenerate
this file routinely; if they render the same value differently the file churns on every save and
produces a diff that means nothing.

**Before overwriting, check for content that is not derivable from frontmatter** - `Grep` for lines
that are not table rows (do not start with `|`). If there is any, **stop and report it** rather than
destroying it. Regenerating from frontmatter deletes anything that exists only here, which is why
narrative must never live in this file.

## No other column

**Only the seven columns the index shape names.** A work item holds no workflow state, so there is
no `status`; recency is derived, so there is no `updated`; `created` and `type` stay in
`requirements.md`, found by grep.

## When it changes

**Only at mint, or when an item's identity, relations or tags change.** Every other save leaves this
file byte-identical, so the regeneration step is a genuine no-op rather than a rewrite that moves one
date. A save that reports this file as changed when none of `id`, `ids`, `title`, `projects`, `parent`
or `tags` moved is a defect in the writer, not a property of the store.

**Tags are the one column a routine save can legitimately move**, because `/nk:save` both accepts
`--tag` and picks tags up from the session. That is a real change to identity, not churn - name it
when it happens.
