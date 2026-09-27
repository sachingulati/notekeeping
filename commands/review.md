---
description: Read the store's content and propose what should change. A yes applies the proposals; --apply applies a saved report.
argument-hint: "[project] [--since <date>] [--apply [<report>] [all | <numbers>]] [--dry-run] [--page | --no-page] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git log:*), Bash(git ls-files:*), Bash(git -C:*), Artifact
---

Read the store and propose what should change. **It proposes, then asks once: a plain yes applies
every applyable finding exactly as proposed**, per `${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s
`## A yes applies what was shown`, and an answer can narrow it - *"yes, but not 3"*. **Every proposal is saved as a report, and
`--apply` applies a saved report** - all of it, the findings you name, or one at a time - in this
session or any later one. Nothing is written without one or the other.

**Resolve the store first**, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. Read inside
it, plus the registered repository when a finding needs a path checked against it - finding 5 is the
only one that does. Write nowhere else; everything outside `.notekeeping/` belongs to the user.

**`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

## What this is, against `/nk:doctor`

| | `/nk:doctor` | this |
|---|---|---|
| Asks | *is it broken?* | *is it still good?* |
| Reads | structure, headers, paths | **entry text - the content itself** |
| Cost | cheap, run it often | a real pass; run it occasionally |
| Output | errors to fix | proposals to accept or decline |

**Five of the ten findings below are also `doctor` findings.** `doctor` sees them structurally and
cheaply - *this register is past its threshold* - and says so
continuously, so this command is never the first time you hear that something needs attention. What
it adds is the reading: **which topic dominates the register, so which `areas/` the split creates.**
A finding `doctor` can state, this command has to justify from the entries.

## Scope, and why it has one

**With no scope the pass covers the whole store**, which on a mature store is not something to run on
a whim - say how many files it will read and roughly what that costs before starting, and let the
user narrow it.

`[project]` limits the pass to one project's files.

### `--since <date>`

**It narrows the *subject*, and it is not free of charge.** The entries added or changed since the
date are what the pass is about. Two things follow, and both must be said out loud rather than
discovered by the user.

**First, what it saves.** Cost falls only where something can be skipped **without reading it**:

| Bound | When |
|---|---|
| **`git log --since=<date> --name-only`** gives the changed set | the store is a git work tree and git answers - `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, which has **three** git outcomes, not two |
| **the bucket path** - `work/<YYYY-MM>/` is the month of first work, so buckets before the date's month hold no newer work item | always, and it needs no git |

**Where neither applies, the file is read.** A register's entries carry their dates *inside* it, so
narrowing to recent entries still costs the whole file. **Say what was skipped and what still had to
be read**, and never report a narrowed pass as though it were cheap when it was not.

**Second, and this is the sharper half: `--since` changes which findings are possible.** Five of the
ten need more than the subject set, so a narrowed pass **cannot** produce them:

| # | Why a recency filter cannot see it |
|---|---|
| **1 merge** · **7 promote** | both need **a pair**. With one side outside the window there is nothing to compare, and the pass reports *no duplicates* while sitting next to one |
| **2 split** | the threshold is a count of **the whole register**, and a subset cannot exceed a total |
| **3 area** | the test is **spanning three or more month buckets**, which a window shorter than that excludes by construction |
| **9 close** | **inverted.** It looks for a work item with *no* activity; `--since` selects for activity. A recency filter can never surface it |

**Name the suppressed findings before the pass runs, not in the report afterwards** - a user who
wanted a quick look should learn that half the command is off *while they can still change their
mind*, and a pass that silently drops five findings reports *nothing found* in a store that has
plenty.

**Findings 4, 5, 6, 8 and 10 read one entry at a time and are unaffected**, which is what makes the
flag worth keeping.

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
| 7 | The same fact in two projects | **promote** to the workspace |
| 8 | A `decisions.md` entry whose `Would reopen if:` has plausibly fired | **flag it for a human** - never reopened automatically |
| 9 | A work item with no activity and no closure for months | **close it, or say why it is open** |
| 10 | `NOTES.md` holding something needed on one task in ten | **demote it** to the file it belongs to |

### When one entry earns two findings

**This is ordinary, not exotic**, and it must resolve the same way every run. An entry citing a
path that no longer exists, which also makes the same claim as another entry, satisfies **5** and
**1** at once.

**The order is fixed, and it runs from whether the entry should exist to how it should be
arranged:**

| Rank | Findings | The question it answers |
|---|---|---|
| **1** | **5 retire** | should this entry exist at all? Its subject is gone |
| **2** | **6 demote** · **7 promote** · **10 demote from `NOTES.md`** | is it in the right place? |
| **3** | **1 merge** | is it arranged well where it is? |

**The higher rank wins, and the reason is not a preference.** Merging two entries about code that no
longer exists is work spent tidying something the next finding proposes to retire; placing an entry
correctly is worth doing before deciding whether it duplicates its new neighbours. **A proposal that
the following proposal would undo is not a finding, it is churn.**

**Report the winner, and name what it displaced, in one clause** - *"retire; this pair would also
merge, which is moot if they go"*. Nothing is lost, the user can see both, and **the count stays the
count** because one entry produced one finding.

**2 split** and **3 area** never compete here: they are findings about a **register**, not about an
entry, so an entry can earn one of those and one of the above without contradiction. **4**, **8** and
**9** are about a contradiction, a decision and a work item respectively, which are not entries in a
register either.

**And the evidence that tells two findings apart is not optional. Source is the whole test** that
separates a merge from one entry written twice - so a merge proposal, and any refusal of one,
**prints both provenance stamps side by side**. A sentence asserting a shared source while showing
two different ones cannot survive its own output.

### What each one requires before you may report it

- **1 - merge.** The two entries must make *the same claim*, not cover the same topic. Different
  sources is what makes it worth merging; **same source is one entry written twice**, which is a
  duplicate, not a merge. **Quote both claims and both provenance stamps side by side** - the source
  is the test, so it is shown rather than asserted, in a refusal exactly as in a proposal.
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
  old entry. **Gone takes two answers**: `git ls-files -- <path>` prints nothing, so it is not
  tracked, and neither `Read` nor `Glob` finds it, so it is not on disk untracked either. Either one alone
  is not gone. **Never delete**: retirement marks superseded and leaves the text.
- **6 - demote.** The entry sits at workspace or global scope and its provenance names exactly **one**
  project. Provenance naming two is correctly placed.
- **7 - promote.** The **same fact**, not the same subject. Where one project `depends_on` the
  other and the shared fact is the contract between them, it is not a promotion: a contract is read
  from the dependee's code, not kept in the store, so say that and propose nothing.
- **8 - flag.** The trigger must have *plausibly fired*, and you say what you saw that suggests it.
  **You never reopen a decision**, and you never write the reversal - both halves stay visible and
  that is a human's call - a ledger records reversals, it does not overwrite them.
- **9 - close.** No activity and no closure. **A work item explicitly marked open with a stated
  reason is not stale**, however old - that is the `## open <date>` block in its `session.md`, and the
  closing block is its twin. **Both shapes are defined in that file's definition**
  (`${CLAUDE_PLUGIN_ROOT}/reference/schema/files/work/session.md`, *Closing, and staying open*):
  one dated heading and one line of reason. **Write that block and nothing else** - `--apply` here
  appends a known shape, never improvised prose, and it never edits what the item already says.
- **10 - demote from `NOTES.md`.** The entry must be genuinely niche. `NOTES.md` is the
  always-loaded file, so this is the one finding where being wrong costs the user on every single
  task; require a clear case and decline the marginal one.


**The report can be a page.** Where there is enough to choose between, offer it at the very
end, per `${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md` - which owns the offer, the two
flags, and what the page may carry. **The terminal report is printed either way.**

## Report, and what a proposal looks like

Group by finding type, most confident first. Each proposal carries:

```
<type> · <what you read>
  <the entries, quoted enough to judge>
  → <the proposal>
  why: <the rule above that it satisfies>
```

**An applyable proposal names its write** - the file, and what changes in it - because that line is
what a plain yes approves. A proposal that only says *merge these* has not shown what will be written.

**Say what you read and found nothing in.** A pass that reports four findings over 60 files should
say it read 60; otherwise a clean register is indistinguishable from one nobody looked at.

**Number the findings as you print them, and the count is the last number** - one list, nothing
reported outside it, and any split into categories printed as arithmetic that reconciles to it.
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`.

## `--apply`

**Applyable means there is a store write to make and you are allowed to make it - eight of the ten.**
1 merge - 2 split - 3 area - 5 retire - 6 demote - 7 promote - 9 close - 10 demote from `NOTES.md`.

**4 and 8 are not withheld, they have nothing to write.** 4's proposal is to *run a check*, and your
tools are the ones declared above - you cannot run an arbitrary check, and recording *what
was checked and what it returned* would mean inventing it. 8's proposal **is** the flag: applying it
would mean writing the reversal, which you never do. Report both as proposals under every flag.

### `--apply` applies a saved report

**Every pass saves its report**, and `--apply [<report>] [all | <numbers>]` applies it, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s *The report is saved*. **A pass never applies its own findings on a flag** - `--apply` reads a
report that already exists, the latest one when none is named, and **the pass does not run again**:
the findings are the report's, so a number means what it meant when it was printed. The finding set is
not stable between identical passes, which is exactly why the report and not a new pass is what gets
applied.

**The walk - `--apply` with no selection** - takes the report's applyable findings in order, shows
each diff, and offers *apply*, *skip* or *stop*. *Stop* keeps what was applied. **A skipped finding is
not resolved**: record nothing about it; the next pass finds it again, which is correct - declining a
proposal is not answering it. **Report the split at the end**: *applied 3 of 8, skipped 5, 0 stale*,
counted from what you wrote.

### When there is no turn to answer in

**Write nothing, and save the report.** Say which findings were applyable and that nothing was written
because no selection was made. **This is not a degraded mode, it is the correct one**: a selection is
an input you cannot supply on the user's behalf. The report is what lets someone make it later.

### Under every form

1. **Never delete.** Retirement marks an entry superseded and leaves its text where it is. A merge
   keeps both provenance stamps. A split moves entries into `areas/<topic>/` and leaves a pointer.
2. **A split is not finished by the write.** `NOTES.md`'s `## Read on demand` heading and the
   projection of that scope render from the directory listing, so a new area is invisible to a session until
   something re-renders them. **Name `/nk:project <name>` as the step that completes a project's
   split, and `/nk:doctor --fix` or the next `/nk:save` for a workspace or global one**, for
   findings 2 and 3 alike - an area nothing points at is knowledge that has been moved out of reach.
3. **`--apply` is never implied by another flag**, and `--dry-run` beats it: with both, show
   everything, offer nothing and write nothing.
4. **Apply nothing outside the store**, and nothing the report does not carry.

## Never

- **Never delete anything**, under any flag. Superseding is how this design retires a fact, in every
  file, and this command is not the exception.
- **Never rewrite an entry's claim** while merging. A merge joins two records of one fact; it does
  not restate the fact in your own words.
- **Never invent a finding to fill a category.** Ten types is what exists, not a quota - a store
  with two findings has two. **A proposal you cannot cite the entries for is a fabrication**, and
  this command reads the user's own knowledge, so a wrong proposal costs more than a missed one.
- Never report on anything outside the store, **beyond the path finding 5 verifies against the registered repository.**
- Never touch `requirements.md` bodies, which are write-once evidence, or `decisions.md` entries,
  which are append-only.
