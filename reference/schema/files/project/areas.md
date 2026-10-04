---
file:        areas/
scope:       project
schema:      1
enabled:     true
tier:        core
shape:       container
owner:       nk:review creates it; promotion appends to it
trigger:     three or more entries on one topic across this scope's registers, or a register past its split threshold with one topic dominating it
authority:   original
budget:      none - each file inside keeps the budget of the register it came from
---

## Question
Which topic here has earned a shelf of its own?

## Admission
**Three or more entries on one topic**, across any of this project's registers and whatever their
kind or source - or a topic that
**dominates** a register past its split threshold. **A topic is judged by what the entries say**, never by a shared tag: `q3` or `urgent` on thirty items is a label, not a topic. An area takes that topic's
entries and nothing else.

## Exclusion
A long register with no dominant topic -> it stays one register; length alone earns nothing. A topic
that holds across the workspace -> promote it first, then split at that scope. A topic with fewer
than three entries -> not yet an area: the indirection costs more than it saves. A
work-item's own material -> the bundle, never here.

## Entry format
**An area is a directory, not a file.** `areas/<topic>/` holds the same register files as the scope
above it - `gotchas.md`, `patterns.md` and the rest - carrying only that topic's entries. **Each file
keeps the shape, the entry format and the split threshold of the register it came from**, so an area
splits again exactly as its parent did.

```
projects/<name>/
  gotchas.md              the entries that did not move, plus the pointer
  areas/<topic>/
    gotchas.md            this topic's entries, same format, same threshold
    instructions.md       what to do when the work touches this topic
```

**`instructions.md` is the one file here that did not arrive by splitting.** It keeps the project
definition's shape and admission, with the condition already answered: an area instruction applies
*because the work touches this topic*, so it never states the condition its own path already
carries. **It is not always loaded** - it is true of one task in ten. A session reads it when the
work touches one of the area's keys in `areas/INDEX.md`.

**Instructions alone do not mint an area.** The admission above is unchanged: below three
entries the indirection costs more than it saves. Until the topic qualifies, the instruction lives
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

**An area is reachable through its row in `areas/INDEX.md`** - the keys that send a session into
it, per the area-index definition. **A split writes the area and its row in the same pass**; an area
with no row is knowledge moved out of reach. **The split that creates `areas/INDEX.md` also adds
`NOTES.md`'s two area lines**, per the `NOTES.md` definition - an index nothing reads is the same
loss one level up.
