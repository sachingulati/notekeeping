---
file:        overview.md
scope:       project
schema:      2
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
Fields, not prose - **and a field is a bold label at the start of its own line**, which is the one
thing this definition used to leave to whoever wrote the file:

```markdown
<!-- authority: derived - assembled from the sources below -->
<!-- sources: <build file> - <readme> - <the repo's own CLAUDE.md> -->
<!-- verified: repo <sha> (<date>) -->

**What**         one line: what this project is
**Stack**        languages, frameworks, the versions that matter
**Repos**        every repository this project spans
**Upstream**     what it depends on
**Downstream**   what depends on it
**Entry points** where execution starts - binaries, endpoints, jobs
**Owner**        who to ask
**Links**        tracker, pipeline, dashboards
**Diagram**      one, or a path to one
```

**A label, not a heading per field.** A heading each would grep well and render badly: this file
carries a 5000-byte ceiling, and nine headings spend it on structure rather than on the answer. **`grep -n '^\*\*Stack\*\*' overview.md` finds the field either way**, which is
the whole job a marker has here.

**The header records what it was assembled from**, and that list is what makes the authority claim
honest.

Orientation is mostly rederivable from files the repository already contains, so this file is a
service rather than accumulated knowledge: you do not have to look in six places. It is rebuilt on
request and is never appended to by promotion.

A repository's committed instructions file is a legitimate **source** for these fields, and is never
a deletion criterion for anything.

## Migration

**1 -> 2 - on write** - the fields gain their bold labels. **It is rebuilt rather than converted**:
`/nk:project <name>` reassembles it from the sources named in its header, in the shape above, and the
stamp goes on that write.

**This file is `derived` only where every field came from a recorded source**, which is what its
`authority:` says and what makes the rebuild safe. **Where any field did not, the file is `original`
in that line and the rebuild must carry it** - see below. A rebuild that treats the whole file as
derived drops exactly the lines nothing else holds.

**It rides along because the rebuild was happening anyway** - this is the one command that writes
this file, and it rewrites the whole of it every time.

**Where a field's content did not come from a recorded source it is carried across verbatim**, label
added and wording untouched, and the report names each one. Those are the lines that make this file
`original` rather than `derived`, and a rebuild that dropped them would lose the only copy. **The
rebuild sets the header's `authority:` to match**: `original` when it carried any such line,
`derived` only when it carried none - never the value the old header had.
