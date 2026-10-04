---
name: index
description: Rebuild Notekeeping's work-item index from each item's frontmatter; save and work keep it current. Use when the user asks to rebuild the work-item index, or a work item is missing from it.
argument-hint: "[--oneline]"
allowed-tools: Read, Glob, Grep, Write
---

Force a rebuild of the store's `index.md`. This exists for repair - `/nk:save` regenerates it as a
matter of course.

Before anything else, in order:
1. **`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
2. **Resolve the store** per `${CLAUDE_PLUGIN_ROOT}/reference/store/resolve.md`.
3. **An overlay?** If `<store>/schema/skills/index/` exists, follow `${CLAUDE_PLUGIN_ROOT}/reference/schema/overlays.md`: a `SKILL.md` there replaces the rest of this file, and a file under its `references/` replaces the shipped reference of that name wherever this skill cites it.
4. **Infer before asking**: read what the conversation already states, and ask only what is still open.

Enumerate
work items only from inside the resolved store.

## What it is

A resolver, not a journal. One line per work item, regenerated in full from each
`requirements.md` frontmatter, which stays authoritative.

The shape is `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` and is not restated here - the
columns, how a row is rendered, and what earns a column and what does not.

## What this command adds

Nothing but the rebuild. `/nk:save` and `/nk:work` regenerate this file in the course of doing
something else; this one does it alone, so it is what you run when the file and the folders on disk
have diverged - after a hand edit, a merge, or a bundle moved by hand.

Enumerate from the folders, not from the existing file. The point of a repair is that the
current contents are not trusted. Walk `work/<bucket>/` inside the resolved store -
`Glob <store>/**/requirements.md`, keeping only the hits shaped `work/<bucket>/<item>/requirements.md`
(never a `*` segment before a literal name: `${CLAUDE_PLUGIN_ROOT}/reference/store/walk.md`,
*A pattern that matches nothing*) - read each frontmatter, and build the table from that.

**Before overwriting, check for content that is not derivable from frontmatter** - the rule is in
`index-shape.md` and the answer is always to stop and report rather than destroy.

Report what moved. A repair that says `rebuilt` and nothing else gives no way to tell a no-op
from a rescue: name how many rows were written, and how many differ from what was there.

The shape, filled from the run and never copied from here:

```
Index rebuilt - <store scope>
  rows written    <n>
  rows differing  <n>
  <row id>        <what differs>
```

`rows differing` is `0` printed, never a line omitted - a rebuild that changed nothing is the
outcome this command exists to be able to report.
