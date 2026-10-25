---
doc: unattended
title: A save nobody watches
---

## A save nobody watches

**`/nk:save --unattended` is a save started by something other than the user** - an idle timer, a
scheduled run - **and it runs to its end without a question.** In every other way it is the ordinary
save: the record, promotion and the drain all run, and `--no-promote` is a choice of its own, never
implied. **The flag changes what the skill asks, never what the harness does**: its writes ask for
permission exactly as any save's do.

**Where a save would not ask, it does what a save does** - a confident mint included. **Never
`AskUserQuestion`**: each place a save would ask has its answer here:

| Where a save would ask | Unattended |
|---|---|
| **Which item** - an inference short of confident, an item loaded with nothing decided for it, or work the session could not place: whether it belongs to a target or is an item of its own | **that work is left out**; every target that is clear is saved |
| **An id that does not resolve** | left out, never minted - a save mints one only when asked |
| **A contradiction not mechanically decidable** | both marked unsettled with `Check: the user's call`, per the promotion rule's *A contradiction* |

**Work left out is a question, never only a *Not saved* line.** *Not saved* is for work whose place
is known and is not a record - finished work git holds; work whose place is undecided is the
question a save would have asked.

## How the report ends

**Each question it did not ask comes last but one**, in the asking rule's shape, each option with the
command that answers it - so the user meets it on their return:

> nk:save needs: 1. Does the daily-reports caching work belong to 0003? - 0003 (`/nk:save 0003`) ·
> a new item (`/nk:work`, then `/nk:save`) · neither

**The closing line is always last**, so the reader can tell the run finished, and it says whether
`/clear` is safe:

| The run | Closing line |
|---|---|
| Work the session held is in no record - left out | `Saved 0003. Not safe to /clear yet - <the work> is in no record; answer nk:save needs 1 first.` - `Nothing saved.` in place of `Saved 0003.` where no target was written |
| Nothing new, nothing left out | the nothing-new line below |
| Everything written | the save's safe-to-clear line, earned as on any save |

An id that does not resolve leaves no work out on its own: what the session held about it is the
work, and a session that held nothing about it closes as the rest of the run does.

## Nothing new writes nothing

**Where nothing since this session's last save would change a file, the run writes nothing** - no
work moved, no decision, nothing learned, no memory item. The last save is the latest `nk:save` run
the conversation holds; with none, everything in the session is new. Its report is the lines a save
gives on every run, such as a store behind the schema release, any question it did not ask, and
then the closing line:

> Unattended save: nothing new since the last save.

**A typed prompt that only asked, or a reply nobody acted on, is not new** - the test is whether any
write would differ, not whether turns were taken.

## What it leaves behind

**Every `- ` line it adds to a session block ends with `- unattended`**, so a reader of the record can
tell a checkpoint nobody reviewed from one the user ran.
