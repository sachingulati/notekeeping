---
file:        summary.md
scope:       work
schema:      1
enabled:     true
tier:        core
shape:       document
owner:       nk:summary
trigger:     only when /nk:summary is typed
authority:   original
budget:      3000 bytes
env_axis:    optional
---

## Question
What happened here, explained to someone who was not involved?

## Admission
What was wrong; what changed, in plain words; how to check it; what is still open.

## Exclusion
Anything only a person who did the work would understand. File paths, class names and diffs are
excluded by rule rather than by preference - one pointer at most.

## Entry format
Four sections, plain human language, written so it can be pasted into a comment or a release note:

```markdown
## What was wrong
## What changed
## How to check it
## What is still open
```

Below the four sections, one line naming where the source material came from, and - once the summary
has been published - one line `Page: <url>`. Neither is part of the summary's text, and neither counts
as its one pointer.

It must read standalone and must not assume the reader remembers the work - including when that
reader is you, eight months later. That constraint is what keeps code references out: a path means
nothing to a reviewer, and nothing to you after a long gap.
