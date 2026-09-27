---
description: Rebuild the work-item resolver from frontmatter. Rarely typed - save does this.
argument-hint: "[--oneline]"
allowed-tools: Read, Glob, Grep, Write
---

Force a rebuild of the store's `index.md`. **This exists for repair** - `/nk:save` regenerates it as a
matter of course.

**`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

Resolve the store first, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and enumerate
work items only from inside it.

## What it is

A **resolver, not a journal.** One line per work item, regenerated in full from each
`requirements.md` frontmatter, which stays authoritative.

**The shape is `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` and is not restated here** - the
seven columns, how a row is rendered, what earns a column and what does not. Seven commands touch
this file and **six of them write it** - this one rebuilds it, `save` and `work` regenerate it,
`adopt` writes it where an adoption produced work items, `upgrade` regenerates it after a
migration, `doctor --fix` regenerates a row, and `load` only resolves against it. A shape stated in more than one place drifts.

## What this command adds

**Nothing but the rebuild.** `/nk:save` and `/nk:work` regenerate this file in the course of doing
something else; this one does it alone, so it is what you run when the file and the folders on disk
have diverged - after a hand edit, a merge, or a bundle moved by hand.

**Enumerate from the folders, not from the existing file.** The point of a repair is that the
current contents are not trusted. Walk `work/<YYYY-MM>/` inside the resolved store, read each
`requirements.md` frontmatter, and build the table from that.

**Before overwriting, check for content that is not derivable from frontmatter** - the rule is in
`index-shape.md` and the answer is always to stop and report rather than destroy.

**Report what moved.** A repair that says `rebuilt` and nothing else gives no way to tell a no-op
from a rescue: name how many rows were written, and how many differ from what was there.

**The shape, filled from the run and never copied from here:**

```
Index rebuilt - <store scope>
  rows written    <n>
  rows differing  <n>
  <row id>        <what differs>
```

**`rows differing` is `0` printed, never a line omitted** - a rebuild that changed nothing is the
outcome this command exists to be able to report.
