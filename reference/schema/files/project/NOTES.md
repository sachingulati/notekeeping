---
file:        NOTES.md
scope:       project
schema:      3
enabled:     true
shape:       document
owner:       nk:project
trigger:     always, per registered project
authority:   original
budget:      12000 bytes
---

## Question
What do I need to know here on almost every task, and where is everything else?

## Admission
Would I need this on most tasks in this project? Plus a pointer to every on-demand file the project holds - an
enabled definition with a file on disk, `overview.md` included - each with its definition's `## Question` as its one-line description, **and the read lines: one to `instructions.md`, and the two area lines to `areas/INDEX.md`**, each where its file exists.

## Exclusion
Needed on one task in ten -> the on-demand file it belongs to, cited from here. Current work state ->
the work item's `resume.md`, or the latest `next:` line in its `session.md` before there is one,
**reached by `/nk:load`, never copied or pointed at from here** - which item is current is derived. **Something to do rather than
something that is true** -> `instructions.md` at this scope, which this file's read line points at. An
area's own content -> the area; `areas/INDEX.md` maps the keys to it.

## Entry format
```markdown
# <project>
<!-- NOTES.md - What do I always need here, and where is everything else? -->
<!-- authority: original -->
<!-- verified: repo <sha> (<date>) - env <name> <build> (<date>) -->

## Read first
Before answering anything or starting any task here, read <store>/projects/<project>/instructions.md.
Before answering anything or starting any task here, read <store>/projects/<project>/areas/INDEX.md - it lists which topics have an area of their own, by key. Whenever the work touches one of those keys - at the start or partway through - read that area's files before going on.

## Always needed
build - run - ports - naming - the one or two traps that bite every time

## Read on demand - <store>/projects/<project>/
overview.md - <its definition's ## Question, as one line>
gotchas.md - <its definition's ## Question, as one line>
```

**A stamp's date is the day it was written** - the session's date when the stamp went on, never a
commit's date. The sha is read per the repo-facts rule's *the commit*; unreadable writes the repo
half *unverified*.

**The read lines are written whole, with absolute paths, and only for a file that exists.** Each
is worded *before answering anything or starting any task here, read ...* - a line worded *before
working* is skipped when the session is only asked a question. **A skill that creates
`instructions.md` or `areas/INDEX.md` adds its line here in the same pass.** Areas are not listed
here: `areas/INDEX.md` is their map, one row per area, and the second area line is what sends a
session into one - **whenever the work touches a key, at the start or partway through**.

**`<store>` is the resolved workspace store - `<workspace>/.notekeeping/` - and it is written out
in full, never store-relative.** A session reads this file from the repository, through the
read line, so a store-relative path would not resolve from where it sits - and a path that does not
resolve produces a read that is refused and then improvised around.

**A write that leaves this file at or past `budget_notice_pct` of its budget says so**, per
the budget-notice rule.

**`/nk:project <name>` rebuilds this file.** `/nk:init` creates it and `/nk:adopt` writes it while
building the store; `/nk:save` maintains its verified stamp, and
promotes into `## Always needed`; `/nk:review` can demote an entry out of it. **A person, or an agent
outside the plugin, may edit it too**: a rebuild re-derives only the derived sections and carries
everything else across.

The governing test is content, not size. The budget exists only so this file cannot quietly become
the notes.

## Migration

**1 -> 2** - nothing changes; this file's shape did not move at release 2.

**2 -> 3** - remove the `## Active` heading and the lines under it, up to the next `## ` heading or
the end; change nothing else. A file with no `## Active` section only gains its stamp. A file whose
sections cannot be told apart is left alone, unstamped, and named in the report.
