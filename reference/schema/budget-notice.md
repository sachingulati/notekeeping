---
doc:   budget-notice
title: The budget notice
---

# The budget notice

**Any command writing a file whose resolved definition carries a `budget:` says so when the write
leaves it at or past `budget_notice_pct` of that ceiling** - the share is in
the config defaults, and it defaults to 80.

**It applies to every definition whose `budget:`
is a ceiling** - bytes, or a register's split threshold in entries. **A `notice at <n> bytes`
budget** - `work/resume.md`'s - has no share to take: the line is printed when the write leaves the
file past `<n>`, without a percentage. `budget: none` is uncapped and prints nothing.

**Measure what you just wrote, not the file on disk** - the writer is holding the content, so
nothing needs to stat the file.

**Where the write was an append or an edit**, the figure is the resulting whole - what was there
plus what went in - and where that is not known exactly, **say it is approximate rather than
omitting the line.** An approximate notice is the signal; a missing one reads as *under budget*.

**One line, and it repeats.** Being over budget is a standing condition rather than an event, so the
notice appears on every write above the line and never grows past a line:

```
NOTES.md is at 10,400 of 12,000 bytes (87%)
```

**A notice is not a refusal and not a repair.** Nothing is truncated, nothing is dropped, and the
write completes - what a file does when it passes its ceiling is the definition's business. **Say the number, not the
remedy**: the file's own exclusion test is what decides that, and proposing a demotion here would
guess at it.
