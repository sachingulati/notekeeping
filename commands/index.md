---
description: Rebuild the work-item resolver from frontmatter. Rarely typed - save does this.
argument-hint: "[--caller <name>]"
allowed-tools: Read, Glob, Grep, Write
---

Force a rebuild of the store's `index.md`. **This exists for repair** - `/nk:save` regenerates it as a
matter of course.

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: never ask - refuse naming the argument
that would satisfy it - and end with the outcome line.

Resolve the store first, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and enumerate
work items only from inside it.

## What it is

A **resolver, not a journal.** One line per work item, regenerated in full from each
`requirements.md` frontmatter, which stays authoritative.

**The shape is `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` and is not restated here** - the
seven columns, how a row is rendered, what earns a column and what does not. Four commands touch
this file and two of them write it; a shape stated in more than one place drifts, and it already
has once.

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
