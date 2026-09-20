---
file:        areas/
scope:       project
schema:      1
enabled:     true
tier:        core
shape:       container
owner:       nk:review creates it; promotion appends to it
trigger:     a register passes its split threshold and one topic dominates it
authority:   original
budget:      none - each file inside keeps the budget of the register it came from
env_axis:    optional
---

## Question
Which topic here has earned a shelf of its own?

## Admission
A topic that already **dominates** a register past its split threshold, or a thread whose promotions
span **three or more** `work/<YYYY-MM>/` buckets. An area takes that topic's entries and nothing
else.

## Exclusion
A long register with no dominant topic -> it stays one register; length alone earns nothing. A topic
that holds across the workspace -> promote it first, then split at that scope. A topic with a handful
of entries -> not yet an area: below the threshold the indirection costs more than it saves. A
work-item's own material -> the bundle, never here.

## Entry format
**An area is a directory, not a file.** `areas/<topic>/` holds the same register files as the scope
above it - `gotchas.md`, `patterns.md`, `decisions.md` and the rest - carrying only that topic's
entries. **Each file keeps the shape, the entry format and the split threshold of the register it
came from**, so an area splits again exactly as its parent did.

```
projects/<name>/
  gotchas.md              the entries that did not move, plus the pointer
  areas/<topic>/
    gotchas.md            this topic's entries, same format, same threshold
    decisions.md
    instructions.md       what to do when the work touches this topic
```

**`instructions.md` is the one file here that did not arrive by splitting.** It keeps the project
definition's shape and admission, with the condition already answered: an area instruction applies
*because the work touches this topic*, so it never states the condition its own path already
carries. **It is not projected** - the always-loaded block would pay for it on every task, and it is
true of one task in ten. What loads it is either the catalogue below or an entry in the project's
`instructions.md` naming this area, which is the deliberate upgrade for a topic that has earned one.

**Instructions alone do not mint an area.** The admission above is unchanged: below the split
threshold the indirection costs more than it saves. Until the topic qualifies, the instruction lives
in the project's `instructions.md` with its condition stated in its own text, and moves here when
the area appears.

**`<topic>` is a directory-safe slug.** Where a tag already names this thread, **reuse that
spelling** - a `tags` value and an area name for one subject that differ by a character are two
threads to everyone who comes later.

**A split moves entries and leaves a pointer.** The parent register keeps a line naming the area and
what went into it. **Nothing is deleted and nothing is reworded**: entries move with their provenance
stamps and their corrections intact, which is what makes a split reversible by hand.

**Areas do not nest.** `areas/<topic>/areas/` is not a thing - a topic that has outgrown its area is
a sign the topic was drawn too wide, and the answer is a second area beside it.

**An area is not reachable until something renders it by name.** `NOTES.md`'s `## Read on demand`
heading and both projections render from the directory listing, so an area created by a split is
invisible to a session until `/nk:project <name>` rebuilds them. **A split is finished by that
rebuild**, not by the write.

**One line per area, never a bare `areas/`.** Naming the container says only that shelves exist,
which leaves finding the right one to relevance recall - the mechanism measured as silently failing
in `${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, *What the on-demand half renders*. Each area
renders as its own line, carrying the cues that identify its topic, so a session can tell whether
this is the shelf the work needs before paying to open it.
