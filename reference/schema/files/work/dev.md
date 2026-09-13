---
file:        dev.md
scope:       work
enabled:     true
tier:        core
shape:       document+append
owner:       nk:save
trigger:     work actually starts
authority:   original
budget:      handover 15 lines / 1200 bytes; session blocks none
env_axis:    optional
---

## Question
Where does this stand, and what happened?

## Admission
Anything that happened, dated. The handover block holds current state only.

## Exclusion
A fact that outlives this item -> promote it. Narrative longer than about fifteen lines -> the
session block, not the handover.

## Entry format
Two regions with different owners. The handover is rewritten on every save; the session blocks below
it are append-only and are never edited.

```markdown
## Where things stand
<!-- rewritten every /nk:save - this is what /nk:load reads to restore context -->
**Branch**    <branch> - <n> ahead of origin - <n> dirty files
**Done**      what is finished and verified
**In flight** what is started, and where it is
**Next**      the next decision or action
**Blocked**   what is stuck, and on whom
**Verify**    the command that proves it works

---
## <date>
- what happened, in order
```

Re-running save on the same day extends today's block rather than opening a second one.
