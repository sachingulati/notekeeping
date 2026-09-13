---
file:        NOTES.md
scope:       project
enabled:     true
tier:        core
shape:       document
owner:       nk:save
trigger:     always, per active project
authority:   original
budget:      12000 bytes
env_axis:    optional
---

## Question
What do I need to know here on almost every task, and where is everything else?

## Admission
Would I need this on most tasks in this project? Plus a pointer to every on-demand file, each with a
required one-line description.

## Exclusion
Needed on one task in ten -> the on-demand file it belongs to, cited from here. Current work state ->
the handover block in dev.md, **pointed at, never copied**.

## Entry format
```markdown
# <project>
<!-- NOTES.md - What do I always need here, and where is everything else? -->
<!-- authority: original -->
<!-- verified: repo <sha> (<date>) - env <name> <build> (<date>) -->

## Active
<work-item id> -> work/<bucket>/<id>/dev.md          <- one line, a pointer

## Always needed
build - run - ports - naming - the one or two traps that bite every time

## Read on demand - <store>/projects/<project>/
gotchas - patterns - decisions - domain - runbook - architecture - interfaces - areas/
```

**`<store>` is the resolved workspace store - `<workspace>/.notekeeping/` - and it is written out
in full, never store-relative.** This heading is what the repo projection's own `Read on demand`
line renders from, and a path that does not resolve from where that file sits produces a read that
is refused and then improvised around.

**Over budget, degrade - never truncate.** A file past its ceiling is delivered as a pointer and its
size, not as a silently shortened version. A budget that fails visibly is the difference between a
small file and a wrong one.

The governing test is content, not size. The ceiling exists only so this file cannot quietly become
the notes. Never hand-edited.
