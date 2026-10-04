---
doc: item
title: Resolving a work item
---

# Resolving a work item

**An exact `id` or `ids` match is the item.** Otherwise grep the query across `index.md`'s `id`, `title`, `tags` and `folder`, case-insensitive, a multi-word query as one phrase; every matching row is a candidate. **One is the target; several are listed newest first, to pick.** **No match says so** and names what was searched; nothing searches the file bodies. **Given nothing, infer from the conversation**, the branch name one input to it, and **resolve that inference against `index.md`**, never against memory.

**Newest is derived** - the newest dated block in each candidate's `session.md`, falling back to its `work/<bucket>/` bucket. One cheap read per candidate, never per item.

**State the inference you made, and name the store path you used**, so a wrong root is visible immediately rather than after acting on it.

## No `index.md`?

It is derived, and a store that has never completed a save has none - so its
absence is not an answer about the work. **List `work/<bucket>/`** - `Glob <store>/**/requirements.md`,
keeping only the hits shaped `work/<bucket>/<item>/requirements.md` - **and resolve against that**, and **say you resolved against
the directory**.
