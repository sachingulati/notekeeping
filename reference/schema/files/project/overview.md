---
file:        overview.md
scope:       project
enabled:     true
tier:        core
shape:       document
owner:       nk:project
trigger:     always, per active project
authority:   derived when every field came from a recorded source; original otherwise
budget:      5000 bytes
env_axis:    optional
---

## Question
What is this project, in fields?

## Admission
A field in the orientation set: stack - language and build versions - packaging - role - repos
spanned - upstream and downstream - entry points - owner - links - one diagram.

## Exclusion
A *reading* rather than a fact -> architecture.md. Anything operational -> runbook.md. Anything that
would surprise you -> gotchas.md.

## Entry format
Fields, not prose. The header records **what it was assembled from**, and that list is what makes the
authority claim honest:

```markdown
<!-- authority: derived - assembled from the sources below -->
<!-- sources: <build file> - <readme> - <the repo's own CLAUDE.md> -->
<!-- verified: repo <sha> (<date>) -->
```

Orientation is mostly rederivable from files the repository already contains, so this file is a
service rather than accumulated knowledge: you do not have to look in six places. It is rebuilt on
request and is never appended to by promotion.

A repository's committed instructions file is a legitimate **source** for these fields, and is never
a deletion criterion for anything.
