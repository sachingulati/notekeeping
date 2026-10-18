---
file:        inventory.md
scope:       work
schema:      4
enabled:     true
shape:       bookkeeping
owner:       nk:adopt, nk:doctor, nk:review, nk:upgrade - the run engine
trigger:     a run item is minted
authority:   original
budget:      none - grepped, never read whole by another run
---

## Question
What did this run look at, and how far did it get with each?

## Admission
One row per source or subject the run's inventory listed - a file it read or left, a check it ran,
a definition it surveyed - with its status, and its size and hash where it is a file.

## Exclusion
What the run found -> `report.md`. What it wrote -> `log.md`. Why a source was worth reading ->
nothing; a row is a fact about the run, not about the knowledge.

## Entry format
```markdown
<!-- nk: schema 4 -->
| subject | status | bytes | hash | note |
|---|---|---|---|---|
| ~/.claude/CLAUDE.md | read | 8412 | 7cb6d35b663b543fb6a10485a878afe9853c0b99 | 18 items |
| ~/notes/team/runbook.md | pending | 20480 | | pointed at by ~/.claude/CLAUDE.md |
| ~/notes/a.md | drafted | 3310 | 51d0c2e8a7f4b1e9c03d6a2f5b8e7c1d9a0f3b62 | drafts/2.md |
| ~/old-wiki/ | declined | | | dropped from the wave |
| doctor · index-orphan | checked | | | 2 items |
```

**`subject` is a path as the run resolved it, `~` kept as written, or a command's own name for what
it checked.** **`bytes` and `hash` are the file-facts rule's *size* and *content hash*** - the hash as
the run read the file, so a later run can tell an edit. A fact the run could not get is an empty
cell.

| Status | Means |
|---|---|
| **read** | read whole in this run's pass |
| **drafted** | read in a subagent, its drafts in the file `note` names - a pass that stops resumes from the rows not yet here |
| **pending** | found and not read - past a limit, or not yet reached - and left for the next run |
| **declined** | a root the user dropped |
| **checked** | a check or a definition the run went through, with no file of its own to read |
| **skipped** | unchanged since an earlier run read it - `note` names that run |

**A row's status is the one cell rewritten** - `pending` to `drafted` to `read` as the pass reaches
it. **Another run finds a source by `Grep` on `^\| <path> \|`** across the runs of its command, newest
first: the newest row decides whether it was read and with which hash.

## Migration
**3 -> 4 - gather.** A release-3 store records adopt's reads in `adopted.md`, one at the root of each
store that ran an adopt that applied. It is outstanding while it exists. Run this step **after**
`report.md`'s, which mints the run items its rows belong to.

- **Each row moves into the run item its `report` column names** - the item converted from
  `tmp/<report>.md`, found by the title's `from` name - as a row of that run's `inventory.md`: `path`
  to `subject`, `status`, `bytes` and `hash` as they are, and `note` the row's `date` and its
  `outcome` - `<date> · <outcome>`.
- **Rows whose report has no run item** - the report was deleted by hand, or swept - go to one adopt
  run item minted for them, *Adopt runs before release 4 (from adopted.md)*, its `created` the
  earliest row's date.
- **The newest adopt run's `resume.md` names the pending set** - every path whose last row is
  `pending` - so the next `/nk:adopt` continues there.

**`adopted.md` is copied byte for byte into the upgrade run's `copies/` before it is removed** - the
file-facts rule's *move a converted source* - and only once every row is in a run item. A row already
present in its run item is not written twice, so a step stopped part-way is finished by re-running.
