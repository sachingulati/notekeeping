# `resume.md` - how a save rewrites it

**This is the single home of current state and of the story behind it**, and it is what a cold
session reads first. Its regions and their rules are the `resume.md` definition's, and are not restated here. What this
command owes it is **how** it is rewritten.

**`## Where things stand` is regenerated every save; everything below it accumulates.** The position
is only ever about now, so it is written fresh; the story, the decisions and the rejected list are
extended.

**About fifteen lines for the position.** Longer narrative belongs in today's session block, not in
that region. Nothing else holds a copy of it - two copies of "where things stand" drift, and the
copy is what the next session trusts.

## Re-derive it where you can, carry it forward where you cannot

| This session loaded | Rebuild the story from | `Covers` says |
|---|---|---|
| **all of `session.md`** (a `--full` load) | **the record, which is already in context** | `re-derived in <this session's id>` |
| the latest block only (a `--quick` load) | the previous `resume.md`, this session, and that block | `carried forward since <id>` - the id the previous `Covers` names |
| **no load at all** (`/nk:load` never ran this session) | the previous `resume.md` alone | `carried forward since <id>` - the id the previous `Covers` names |

**Re-deriving is what stops the file drifting**, because each rebuild is anchored to an append-only
record rather than to the last rebuild - ten carried-forward saves is ten compressions of a
compression. **Where the history is in context it costs nothing**, which is the whole reason
`--full` exists.

**Never read `session.md` to re-derive.** A save happens when the context is already filling, a
second read appends a second copy at full price, and pulling history in at that moment risks
compacting the very material being summarised. If this session did not load it, carry forward and
say so - that is what `carried forward` is for, and `/nk:doctor` is what notices a run of them.

**The first `resume.md` is always re-derived.** It is written at the fifth saving session, when
`session.md` holds four sessions and this one. A `/nk:load` of an item with no `resume.md` reads
`session.md` whole, so the record is usually in context already; where it is not, read it this once -
it is bounded by those few sessions, which is what the threshold is for.

## The branch line

**The branch is the repo-facts rule's *the branch*.** A detached `HEAD` names no branch: write
`detached`; unknown - git unavailable and the fallback empty - write `unknown`. Write the branch alone.
