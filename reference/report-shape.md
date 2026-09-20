---
doc:   report-shape
title: How a report states what it found, so a count cannot disagree with a list
---

# Report shape

**Every command that reports a set of things - findings, proposals, repairs, conversions - states
how many.** That number has disagreed with the list beside it **three times in recorded runs**, and
every one of those runs was made by a build that already said *never report a count you did not
derive*. **The instruction is not the remedy**, because it asks for care at the exact moment care is
scarce. This file replaces it with a shape in which the disagreement has nowhere to live.

## The three rules

**1 · One numbered list, and everything reported is in it.**

Number the items as you print them, from `1`. **Nothing that counts as a finding is printed outside
the list** - not in a preamble, not in a paragraph before it, not as an aside afterwards. The
recorded failure was exactly this: a contradiction finding was described two paragraphs above the
list, so it was reported but never enumerated, and the summary then counted the list.

**2 · The count is the last number you printed. It is never formed separately.**

There is no second act of counting to get wrong. If the list ends at `11`, the answer to *how many*
is `11`, read off rather than totalled. **A number arrived at any other way is not a count**, however
carefully it was added up.

**3 · A split into categories prints its arithmetic, and it reconciles to that number.**

Never `8 are applyable and 2 are report-only` beside a list of eleven. Write the sum:

```
11 findings - 8 applyable, 3 with nothing to write
```

**The parts must add to the whole, visibly.** Where they do not, the error is in the categories and
the list is right - **re-derive the categories, and never adjust the total to match them.**

## What this does not cover

**A count of things you did rather than things you found** - files converted, lines removed, entries
written - is counted **from what you actually did**, after doing it. Never from the plan, never from
the proposal, and never from the survey that preceded it: the gap between *proposed* and *done* is
the thing such a number exists to expose.

**A count of things you did not read** - a survey's `outstanding`, a skipped definition - is derived
from the glob or the grep that produced it, and the report says which. It is evidence that a pass
cost what it should have, so an estimate defeats its whole purpose.

## Why the shape rather than the warning

**A step the model can skip is a step it will sometimes skip** - as when a
confirmation was replaced by a selection because a selection is an input that cannot be
manufactured. **A total is the same kind of hazard**: stated separately, it is a claim nobody
checks against the thing it summarises. Made the last line of the list, it is not a claim at all.
