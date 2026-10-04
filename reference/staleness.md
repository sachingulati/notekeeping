---
doc:   staleness
title: Staleness, on two axes
---

# Staleness

**A file's staleness is read off its `verified:` stamp, on two axes**, and reported - never judged.

```markdown
<!-- verified: repo <sha> (<date>) - env <name> <build> (<date>) -->
```

**A stamp's date is the day it was written** - the session's date when the stamp went on, never a
commit's date, never a guess. The `NOTES.md` and `overview.md` definitions say so, and every writer
of a stamp writes it so.

| Axis | Trips on | Also reports |
|---|---|---|
| **repo** | days since the repo half's date, against `staleness_warn_days` | **moved** - the sha read now against the stamped one |
| **env** | days since the env half's date, against `staleness_warn_days` | nothing. Mandatory on `runbook.md` and `test.md`, optional elsewhere |

**`staleness_warn_days` is resolved per the config-defaults rule and never assumed.** Days are
counted from the stamp's date to the session's date. **Only days trip**, on either axis.

## Moved

**Read the sha now** the way the store walk's *The commit a branch points at* reads it: `.git/HEAD`,
then the ref it names - under `commondir` in a worktree - or the matching line of `packed-refs`. A
detached HEAD holds its own sha. **Compare it with the stamped sha**: equal is `no`; different is
*HEAD moved since `<sha7>`*, the stamped sha's first seven characters.

**Moved trips nothing.** A commit since the stamp is a fact worth seeing, not a verdict: one commit
can touch nothing the file says, and a rebase moves HEAD without changing a line. It is its own line,
never folded into `tripped:`.

## Unknowns

**Unknown is never fresh.** Each of these reads **moved: unknown**:
- a stamped sha reading `unknown`, `<could not determine>`, or nothing - `/nk:init` writes
  `overview.md` with no sha, so a new project's is unknown until its first rebuild;
- a HEAD that cannot be read - the file is missing or unreadable, or its ref is in neither the ref
  file nor `packed-refs`.

**The repository itself has three outcomes**, and the last two are different claims:

| Found | The repo axis reads |
|---|---|
| a `.git`, and HEAD's sha read | `repo <sha7> (<date>)` |
| no `.git` from the starting folder up to the filesystem root | `not a repository` |
| a `.git`, its HEAD unreadable | `could not determine` - and moved is unknown |

The repository is the project's, found per the walk rule's *Which repository*; a workspace or
global store has none, and its files carry the env axis or nothing.

## The lines

```
  staleness     repo <sha7> (<date>) | not a repository | could not determine | unknown
                env <name> <build> (<date>) | unstamped
                moved: no | HEAD moved since <sha7> | unknown
                tripped: <axis> at <days> days against <threshold> | none
```

**`tripped:` names the axis and the days against the threshold, and nothing else** - one per axis
that tripped, `none` when neither did. **Report the axis and the number, never a
verdict**: ninety days is a quiet module in one repository and a rewrite in another.
