---
file:        NOTES.md
scope:       project
schema:      1
enabled:     true
tier:        core
shape:       document
owner:       nk:project
trigger:     always, per active project
authority:   original
budget:      12000 bytes
env_axis:    optional
---

## Question
What do I need to know here on almost every task, and where is everything else?

## Admission
Would I need this on most tasks in this project? Plus a pointer to every on-demand file, each with a
required one-line description, **and one line per area carrying the cues that identify its topic**.

## Exclusion
Needed on one task in ten -> the on-demand file it belongs to, cited from here. Current work state ->
the handover block in resume.md, **pointed at, never copied**. **Something to do rather than something
that is true** -> `instructions.md` at this scope, which is projected beside this file and is not
pointed at from here.

## Entry format
```markdown
# <project>
<!-- NOTES.md - What do I always need here, and where is everything else? -->
<!-- authority: original -->
<!-- verified: repo <sha> (<date>) - env <name> <build> (<date>) -->

## Active
<work-item id> -> work/<bucket>/<id>/resume.md          <- one line, a pointer

## Always needed
build - run - ports - naming - the one or two traps that bite every time

## Read on demand - <store>/projects/<project>/
gotchas - patterns - decisions - domain - runbook - architecture - interfaces

### Areas
- **<topic>** - the cues that identify it -> areas/<topic>/
```

**Every area gets its own line, with the cues that identify its topic** - never a bare `areas/`.
Which areas exist is the directory listing's answer and needs no editing here; what each line says
is this file's, and a shelf named only by its slug is a guess about what it holds.
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, *What the on-demand half renders*, has the rule
and the measurement behind it.

**`<store>` is the resolved workspace store - `<workspace>/.notekeeping/` - and it is written out
in full, never store-relative.** This heading is what the repo projection's own `Read on demand`
line renders from, and a path that does not resolve from where that file sits produces a read that
is refused and then improvised around.

**Over budget, this file degrades rather than truncating** - stated once in
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`, *Budgets, and what to drop*, and not restated
here.

**`/nk:project <name>` rebuilds this file; `/nk:save` maintains its `## Active` pointer and its
verified stamp.** No other command writes it, and it is never hand-edited.

The governing test is content, not size. The ceiling exists only so this file cannot quietly become
the notes.
