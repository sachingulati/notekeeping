---
doc: tags
title: Tags - reuse, mint and announce
---

## Tags

**A tag is a flat label several work items share** - a recurring effort, a theme, a push. An item
carries any number. The field is `tags:` and the resolver's column is `tags`, the same word on both
sides; the index shape defines the column.

**Two sources, and both write the same field.**

| Source | Does |
|---|---|
| `--tag <name>` (`save --tag`, `work --tag`) | **repeatable.** Adds each to the resolved item. Explicit, and never refused - but reuse an existing tag from `index.md` when it matches **ignoring case**, never a synonym, and say which spelling was used. **When a typed tag looks like an existing one, the announcement names it:** *"Tags: `auth` (new). Existing: `authentication`."* Nothing is swapped or asked |
| **the session** | adds tags the session actually named - below |

**Write the tag into the item's `requirements.md` frontmatter, and only then regenerate the index.**
`tags:` lives in `requirements.md`; `index.md`'s `tags` column is **derived from it** and is
authoritative over nothing. **A tag written to the index alone is lost at the next rebuild.**

**This is a permitted edit, not the dated `## Amendment` block `requirements.md` defines elsewhere.**
`requirements.md`'s body is write-once; a tag is a frontmatter field, and touching `tags:` alone does
not rewrite it. Touch the `tags:` key and nothing else in the file.

### Picking tags up from the session

**Read `index.md`'s `tags` column first, and prefer what is already there.** If the session's subject
matches an existing tag, apply **that spelling**.

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
