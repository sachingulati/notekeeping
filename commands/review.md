---
description: Read the store's content and propose what should change. Proposes and stops; --apply shows each diff and you pick what to apply.
argument-hint: "[project] [--since <date>] [--apply [all]] [--dry-run] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git log:*), Bash(git status:*)
---

Read the store and propose what should change. **It proposes and stops.** Nothing is written without
`--apply`, and `--apply` shows each finding's diff and **lets the user choose which to apply and which
to skip**.

**Resolve the store first**, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, and read only
what is inside it. Everything outside `.notekeeping/` belongs to the user.

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md` - and note what it says about this command:
**you report, and you never apply.** Compress the pass into the outcome line's detail, exactly as
`doctor` does, and emit nothing else.

**`--apply` is refused under `--caller`, and `--apply all` with it.** The write depends on a
per-finding selection, and a consumer cannot supply one; a blanket write across the user's own
knowledge with no human choosing is the precise thing the selection exists to prevent. Refuse naming
the reason - the caller can surface the findings to its user, who can then run the pass themselves.

**Emit the outcome line and nothing else.** Explaining the refusal *above* the line is the
narration the contract forbids - the explanation belongs in `<detail>`, on the line itself.

## What this is, against `/nk:doctor`

| | `/nk:doctor` | this |
|---|---|---|
| Asks | *is it broken?* | *is it still good?* |
| Reads | structure, headers, paths | **entry text - the content itself** |
| Cost | cheap, run it often | a real pass; run it occasionally |
| Output | errors to fix | proposals to accept or decline |

**Five of the ten findings below are already `doctor` findings, and that is not duplication.**
`doctor` sees them structurally and cheaply - *this register is past its threshold* - and says so
continuously, so this command is never the first time you hear that something needs attention. What
it adds is the reading: **which topic dominates the register, so which `areas/` the split creates.**
A finding `doctor` can state, this command has to justify from the entries.

## Scope, and why it has one

`[project]` limits the pass to one project's files; `--since <date>` limits it to entries added or
changed since. **With neither, the pass covers the whole store**, which on a mature store is not
something to run on a whim - say how many files it will read and roughly what that costs before
starting, and let the user narrow it.

## The ten findings

Each one names **what you read**, not merely what you concluded. A proposal with no cited entries is
not a finding; say what you looked at.

| # | Finding | Proposal |
|---|---|---|
| 1 | Two entries making the same claim from different sources | **merge**, keeping **both** provenance stamps |
| 2 | A register past its split threshold, with a dominant topic | **split** into `areas/<topic>/` |
| 3 | Several promotions sharing a tag, **spread across three or more month buckets** | **create the area** that thread has earned |
| 4 | An unresolved contradiction whose recorded check was never run | **run it now**, or surface the check |
| 5 | Entries about code that no longer exists - the path is gone | **retire** - mark superseded, never delete |
| 6 | A wide entry whose provenance names one project | **demote** to that project |
| 7 | The same fact in two projects | **promote** to the workspace, or to `interfaces.md` if one depends on the other |
| 8 | A `decisions.md` entry whose `Would reopen if:` has plausibly fired | **flag it for a human** - never reopened automatically |
| 9 | A work item with no activity and no closure for months | **close it, or say why it is open** |
| 10 | `NOTES.md` holding something needed on one task in ten | **demote it** to the file it belongs to |

### What each one requires before you may report it

- **1 - merge.** The two entries must make *the same claim*, not cover the same topic. Different
  sources is what makes it worth merging; **same source is one entry written twice**, which is a
  duplicate, not a merge. Quote both claims side by side.
- **2 - split.** Past the threshold **and** a dominant topic. Past the threshold with entries spread
  evenly is not a split proposal - it is a long register, and saying so is the honest finding. Name
  the topic and the count that makes it dominant.
- **3 - area.** Several promotions sharing one tag - **and the work items they came from must span
  at least three `work/<YYYY-MM>/` buckets.** Name them, the months, and the area the thread earned.
  **The recurrence test is what makes this a finding rather than a word count.** A tag is a general
  label now: `q3` or `urgent` can be on thirty items inside one month and has earned nothing. What
  earns an area is *the same effort coming back*, which is exactly what spanning months shows. Trace
  each promotion to its work item through the provenance stamp, then to its bucket.
- **4 - run the check.** The contradiction must be recorded and its check unrun. **A check that ran
  and disagreed is a different thing entirely** - that is a live contradiction, and it goes to a
  human as-is.
- **5 - retire.** The path must be *gone*, verified against the repository now, not inferred from an
  old entry. **Never delete**: retirement marks superseded and leaves the text.
- **6 - demote.** The entry sits at workspace or global scope and its provenance names exactly **one**
  project. Provenance naming two is correctly placed.
- **7 - promote.** The **same fact**, not the same subject. Where one project `depends_on` the
  other, the target is `interfaces.md` rather than the workspace. **`interfaces.md` ships
  `enabled: false`** - resolve it through
  `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` before proposing, and if it is disabled
  here, **say so and name the overlay line that enables it**, exactly as any command does when it
  meets a disabled definition. Never propose a write into a file the store has switched off, and
  never quietly retarget to the workspace instead - the dependency is what chose `interfaces.md`,
  and the user deciding not to keep that file is a different answer from the fact belonging
  somewhere else.
- **8 - flag.** The trigger must have *plausibly fired*, and you say what you saw that suggests it.
  **You never reopen a decision**, and you never write the reversal - both halves stay visible and
  that is a human's call - a ledger records reversals, it does not overwrite them.
- **9 - close.** No activity and no closure. **A work item explicitly marked open with a stated
  reason is not stale**, however old.
- **10 - demote from `NOTES.md`.** The entry must be genuinely niche. `NOTES.md` is the
  always-loaded file, so this is the one finding where being wrong costs the user on every single
  task; require a clear case and decline the marginal one.

## Report, and what a proposal looks like

Group by finding type, most confident first. Each proposal carries:

```
<type> · <what you read>
  <the entries, quoted enough to judge>
  → <the proposal>
  why: <the rule above that it satisfies>
```

**Say what you read and found nothing in.** A pass that reports four findings over 60 files should
say it read 60; otherwise a clean register is indistinguishable from one nobody looked at.

**Never report a count you did not derive.** Count the findings you are about to print, not the ones
you expected to.

## `--apply`

**Applyable means there is a store write to make and you are allowed to make it - eight of the ten.**
1 merge - 2 split - 3 area - 5 retire - 6 demote - 7 promote - 9 close - 10 demote from `NOTES.md`.

**4 and 8 are not withheld, they have nothing to write.** 4's proposal is to *run a check*, and your
tools are `git log`, `git status` and `ls` - you cannot run an arbitrary check, and recording *what
was checked and what it returned* would mean inventing it. 8's proposal **is** the flag: applying it
would mean writing the reversal, which you never do. Report both as proposals under every flag.

### The user chooses, one finding at a time

1. **Take the applyable findings in the order you reported them**, and for each: **show the diff**,
   then take that finding's decision. **Not every diff first and one question at the end** - a single
   trailing question invites a blanket yes to a list nobody re-read.
2. **Offer three answers per finding - apply, skip, and stop.** *Stop* ends the pass and keeps
   whatever was already applied; say what was applied and what was left.
3. **A skipped finding is not resolved.** Record nothing about it, and do not mark it done. The next
   pass will find it again, which is correct - declining a proposal is not the same as answering it.
4. **Report the split at the end**: *applied 3 of 8, skipped 5*. Count what you actually wrote, per
   the derived-count rule above.

### When there is no turn to answer in

**Write nothing.** Report every finding, say which were applyable, and say plainly that nothing was
written because no selection was made. **This is not a degraded mode, it is the correct one**: a
selection is an input you cannot supply on the user's behalf, and this command edits the user's own
knowledge.

**`--apply all` is the exception, and it must be typed.** It authorises every applyable finding this
pass produced, with no per-finding question. Never infer it, and never treat a bare `--apply` as
meaning it.

**Never accept finding numbers from an earlier run** - `--apply 1,3,7` is refused. The finding set is
not stable between identical passes, so a number from a previous report may name a different finding
now. A selection is only ever taken in the same turn as the report that produced it.

### Under every form

1. **Never delete.** Retirement marks an entry superseded and leaves its text where it is. A merge
   keeps both provenance stamps. A split moves entries into `areas/<topic>/` and leaves a pointer.
2. **`--apply` is never implied by another flag**, and `--dry-run` beats it: with both, show
   everything, offer nothing and write nothing.
3. **Apply nothing outside the store**, and nothing this pass did not report.
4. **A disabled definition still refuses.** Where finding 7 routes a fact to `interfaces.md` and the
   store has switched that file off, selecting the finding does not override it: name the enabling
   overlay line, exactly as the finding's own rule says.

## Never

- **Never delete anything**, under any flag. Superseding is how this design retires a fact, in every
  file, and this command is not the exception.
- **Never rewrite an entry's claim** while merging. A merge joins two records of one fact; it does
  not restate the fact in your own words.
- **Never invent a finding to fill a category.** Ten types is what exists, not a quota - a store
  with two findings has two. **A proposal you cannot cite the entries for is a fabrication**, and
  this command reads the user's own knowledge, so a wrong proposal costs more than a missed one.
- Never report on anything outside the store.
- Never touch `requirements.md` bodies, which are write-once evidence, or `decisions.md` entries,
  which are append-only.
