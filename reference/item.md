---
doc: item
title: Resolving a work item
---

# Resolving a work item

**An exact `id` or `ids` match is the item.** Otherwise grep the query across `index.md`'s `id`, `title`, `tags` and `folder`, case-insensitive, a multi-word query as one phrase; every matching row is a candidate. **One is the target; several are listed newest first, to pick.** **No match says so** and names what was searched; nothing searches the file bodies. **Given nothing, infer from the conversation**, the branch name one input to it, and **resolve that inference against `index.md`**, never against memory. **An inference never resolves to a run item** - one tagged `run-<command>`; a run is reached only by naming it. **With nothing in the conversation to go on**, take the latest date on any open item's session block - open meaning no `## closed` block after it (`work/session.md`, *Closing, and staying open*); **the candidates are only the open items with a block on that date** - an item whose newest block is older is not a candidate and is not offered. One is the target; several tie and are listed to pick, with no favourite - a bucket never breaks a tie between items that both have blocks.

**Several exact ids are several targets**, each resolved as above - for a command that takes more than one (`/nk:save`). A query stays one target.

**Newest is derived** - the newest dated block in each candidate's `session.md`, falling back to its `work/<bucket>/` bucket. One cheap read per candidate, never per item.

**State the inference you made, and name the store path you used**, so a wrong root is visible immediately rather than after acting on it.

## No `index.md`?

It is derived, and a store that no save or `/nk:work` has written to has none - so its
absence is not an answer about the work. **List `work/<bucket>/`** - `Glob <store>/**/requirements.md`,
keeping only the hits shaped `work/<bucket>/<item>/requirements.md` - **and resolve against that**, and **say you resolved against
the directory**.
