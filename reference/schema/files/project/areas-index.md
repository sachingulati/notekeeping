---
file:        areas/INDEX.md
scope:       project
schema:      1
enabled:     true
tier:        core
shape:       register
owner:       nk:review creates it; promotion appends to it
trigger:     an area is created
authority:   original
budget:      none
---

## Question
Which area should a session read, for the work in front of it?

## Admission
One row per area at this scope: the keys - the words a task would use when it touches the topic -
and the area's folder.

## Exclusion
Anything that is not a key or a folder -> the area itself. An area's content, its instructions and
its gotchas live in the area; this file is only the map.

## Entry format
```markdown
# Areas
<!-- areas/INDEX.md - the keys that send a session into an area -->

| Keys | Area |
|---|---|
| <key>, <key>, <key> | <topic>/ |
```

**The area is the folder beside this file**, named by its slug - `<store>/projects/<name>/areas/<topic>/`.
**Keys are words a task would use**, not the slug alone: the topic's names, its synonyms, the
functions and files it is about. A row with one key is a guess about how the topic will be asked
for.

**A split appends the row in the same pass as the area**, and a row is never removed while its
folder exists. Retiring an area is a `/nk:review` finding like any other.

**`NOTES.md` points here** with two lines: read this file before answering anything or starting any
task, and read an area's files whenever the work touches one of its keys. This file holds the map
only, so it stays small enough to read every time.
