# `resume.md` - how a save rewrites it

**This is the single home of current state and of the story behind it**, and it is what a cold
session reads first. Its regions and their rules are the `resume.md` definition's, and are not restated here. What this
command owes it is **how** it is rewritten.

**`## Where things stand` is regenerated every save; everything below it accumulates.** The position
is only ever about now, so it is written fresh; the story, the decisions and the rejected list are
extended.

**About fifteen lines for the position.** Longer narrative belongs in today's session block, not in
that region. The project's `NOTES.md` holds a **pointer** to this file, never a copy - two copies of
"where things stand" drift, and the copy is what the next session trusts.

## Re-derive it where you can, carry it forward where you cannot

| This session loaded | Rebuild the story from | `Covers` says |
|---|---|---|
| **all of `session.md`** (a `--full` load) | **the record, which is already in context** | `re-derived` |
| the latest block only (a `--quick` load) | the previous `resume.md`, this session, and that block | `carried forward` |
| **no load at all** (`/nk:load` never ran this session) | the previous `resume.md` alone | `carried forward` |

**Re-deriving is what stops the file drifting**, because each rebuild is anchored to an append-only
record rather than to the last rebuild - ten carried-forward saves is ten compressions of a
compression. **Where the history is in context it costs nothing**, which is the whole reason
`--full` exists.

**Never read `session.md` to re-derive.** A save happens when the context is already filling, a
second read appends a second copy at full price, and pulling history in at that moment risks
compacting the very material being summarised. If this session did not load it, carry forward and
say so - that is what `carried forward` is for, and `/nk:doctor` is what notices a run of them.

## The branch line

**The branch is `.git/HEAD`'s `ref:` line**, read as a file - in a worktree
through the `gitdir:` line, as the walk rule says - never from git. A detached `HEAD` names no branch:
write `detached`. **The dirty count and the commits ahead of origin are not recorded** - both came
from git, and this plugin runs none. Write the branch alone.
