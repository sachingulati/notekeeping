---
file:        summary.md
scope:       work
schema:      1
enabled:     true
shape:       document
owner:       nk:summary
trigger:     /nk:summary, and the end of every run (the run-engine rule)
authority:   original
budget:      none - the In a minute part is held to 1500 bytes, and a run item's summary to 3000 bytes
---

## Question
What is this work, what was done and why, and what should I check before it ships?

## Admission
What the work is and what caused it; the solution, and how it works now; what was tried and dropped,
and why; the decisions taken, and why; how the pieces connect; the code that changed, from the item's
commits; how it was checked, and what was not; where the work rests on an assumption nobody
confirmed; what is still open.

## Exclusion
A timeline - what happened on which day -> `session.md`, which is the journal; a summary explains the
work, it does not retell it. Anything the item's notes and its commits do not hold: **a summary is a
view of them**, never a source of its own, and is safe to throw away and rebuild.

## Entry format
**The *In a minute* part comes first**, in plain words, short enough to paste into a pull request or
a release note: what the work is, what was done, and what to check before pushing - within 1500 bytes.

**Then one readable story.** Its chapters follow this item - there are no fixed headings to fill, and
a chapter with nothing to say is not written. Simple terms throughout. Most of it is about what was
done and how it works; the problem takes a paragraph, because `requirements.md` holds it. The story
carries:

- **Illustrations wherever they help** - how the pieces connect, before and after, how a flow runs -
  drawn by the summary itself. A screenshot in the item's `artifacts/` may serve where it shows
  something better, and the summary never depends on one being there.
- **Each decision with what it was chosen over**, and an approach that was dropped where it was
  dropped, saying why.
- **Each assumption where it is made** - a choice nobody confirmed is said at that point, as something
  to check.
- **The code behind a map** - what each part of the change does first, then its files and the lines
  that matter.
- **What was asked against how it was checked** - each requirement, where it is met, and how that was
  verified; anything unchecked said plainly.
- **What to check before pushing, and what is still open.**

**Below the story, one line naming its sources** - the notes it read, and the commits it used - and
one line naming the nearby commits it left out as other work.

**Two formats, each written from the notes and commits directly - neither is converted from the
other.** The store's `summary_format` setting chooses (the config defaults):

| Format | Writes | Made for |
|---|---|---|
| `md` | `summary.md` in the item | reading as text - the terminal, a paste; its diagrams plain-text pictures |
| `html` | `artifacts/summary.html` in the item, and no `summary.md` | reading as a page - drawn diagrams, a side menu, folded code, a copy control on *In a minute* |

**A run item rewrites it at the end of every run** (the run-engine rule) - always as `summary.md`,
within 3000 bytes, in four sections and plain human language:

```markdown
## What was wrong
## What changed
## How to check it
## What is still open
```

What the run found, what it changed - naming `copies/` where it changed a file outside the store, since
a revert is copying one back - how to check it, and what it left open or for the next run. Below them,
one line naming where the source material came from. It must read standalone and must not assume the
reader remembers the run.
