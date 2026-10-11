---
file:        adopted.md
scope:       workspace
schema:      1
enabled:     true
shape:       bookkeeping
owner:       nk:adopt
trigger:     the first /nk:adopt run that applies
authority:   original
budget:      none - grepped, never read whole
---

## Question
Which sources has `/nk:adopt` read, which has it found and left for the next run, and which roots did the user decline?

## Admission
One row per path a run read, found and left `pending` at the limit, or was told to drop as a root
(`declined`) - written when that run's report applies, by the report question or a later `--apply`.
A run that never applies records nothing, so the next run reads the same again.

## Exclusion
What a source said -> the entries it produced, in their registers. Why a source was worth reading
-> nothing; a row is a fact about the walk, not about the knowledge.

## Entry format
```markdown
| path | status | bytes | hash | date | report | outcome |
|---|---|---|---|---|---|---|
| ~/.claude/CLAUDE.md | read | 8412 | 7cb6d35b663b543fb6a10485a878afe9853c0b99 | 2026-10-10 | adopt-20261010-1 | 18 written, 9 dropped, 3 unheld, 2 not applied |
| ~/notes/team/runbook.md | pending | 20480 | | 2026-10-10 | adopt-20261010-1 | pointed at by ~/.claude/CLAUDE.md |
| ~/old-wiki/ | declined | | | 2026-10-10 | adopt-20261010-1 | dropped from the wave |
```

**`path` is the file or root as the run resolved it**, `~` kept as written. **`bytes` and `hash` are
the file-facts rule's *size* and *content hash*** - the hash as the run read the file, so the next
run can tell an edit; a `pending` row has a size and no hash, a `declined` root neither, and a
fact the run could not get is an empty cell. A later row for the same
path - a pending source read, an `--again` run - is appended below the earlier one, and **the last
row is the current one**; nothing is edited in place.
**`outcome` names what was not applied**, so a source applied in part shows what its report still
holds; the rest is an `--apply` of that report, never a re-read. Phase 1 finds a source by `Grep`
on `^\| <path> \|`, and the pending set by `Grep` on `\| pending \|` - the last row per path decides -
never by reading the file.
