---
file:        pending.md
scope:       work
schema:      1
enabled:     true
shape:       bookkeeping
owner:       nk:save
trigger:     a `/nk:save --no-promote` that sets a fact aside
authority:   original
budget:      none - a save that promotes empties it
---

## Question
What did a save set aside for promotion, and not promote yet?

## Admission
Each fact a `--no-promote` save would have weighed for promotion - one that outlives this item -
written while the session still held it, per the pending rule.

## Exclusion
What happened -> `session.md`. Where the work stands -> `resume.md`. A fact already promoted ->
nowhere: it leaves this file once its write has landed. A credential -> never here.

## Entry format
```markdown
- <date> · <the fact, whole enough to promote with no session behind it>
- <date> · held <date>: <why> · <the fact>
```

**One line per fact, self-contained** - the save that promotes it has only this line, not the
conversation that taught it. **The date is the day it was set aside.** A fact a promoting save held
back keeps its line with `held <date>: <why>` before the fact, rewritten in place when it is held
again.

**Rewritten whole by `/nk:save`**: a `--no-promote` save adds lines, a save that promotes removes
the ones it settled. **An empty file is nothing waiting.** Nothing else writes it, and nothing sweeps
it as knowledge.
