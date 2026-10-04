---
doc:   overview
title: Rebuilding a project's NOTES.md and overview.md
---

# The rebuild

**Two files, each resolved through its definition before it is written** - the user's overlay wins
over the shipped default. Nothing else in the project is written: registers, ledgers, `areas/` and
`instructions.md` are read freely and never regenerated.

## `NOTES.md` - three derived sections, and everything else preserved

| Section | On rebuild |
|---|---|
| `## Read first` | **derived** - the read lines the `NOTES.md` definition gives, one for each of `instructions.md` and `areas/INDEX.md` that exists, with absolute paths |
| `## Active` | **derived, and one line - the one current work item, never a list.** Keep `/nk:save`'s pointer where the file already has one. Where there is none, point at the project's open item with the newest session block (the index shape's *Recency is derived*) - open meaning no `## closed` block in that item's `session.md`. A pointer, never a copy of the state |
| `## Always needed` | **preserved.** Accumulated knowledge that arrived by promotion, or was written there by a person or another agent, and exists nowhere else. Carry it across unchanged |
| any other section | **preserved** - a person or another agent added it. Carry it across verbatim, in place, and name it in the report |
| `## Read on demand` | **derived** - rendered from the directory listing, so a register added since the last rebuild appears without being told. One line per file: its name and its definition's `## Question` |
| the `verified` stamp | **derived** - *The stamp*, below |

## `overview.md` - rebuilt whole

Every field is assembled: stack, language and build versions, repos spanned, upstream and
downstream, entry points, owner, links, one diagram. **The build file and `README.md` are the sources
for most of them; a repository's own committed instructions file is a legitimate source too** - never
a deletion criterion, and nothing flows from it into a register.

**A field no recorded source supplies is carried across verbatim**, and the report names each one -
the `overview.md` definition says why, and sets `authority:` from it: `derived` only when every field
came from a recorded source, `original` otherwise. **Record the sources in the header's `sources:`
list** - that list is what makes the authority claim honest.

### The repository link

**The origin URL is git's**, per the repo-facts rule's *the origin*. **Strip any `user[:password]@` from the URL before writing
it** - an HTTPS token in a remote is common, and credentials are never copied into the store. A
scp-style remote - `git@host:org/repo` - is written as it is, labelled a remote, not a link. **A
remote not named `origin` is not read**; no origin means no link, and the report says so - and git
unavailable means no link this time, said as that. **It goes in the `Repos` field**, the repositories the project spans - never `Links`, which is for tracker, pipeline and dashboards. Record `git config remote.origin.url` in `sources:` when
it gave the link.

## The stamp

```markdown
<!-- verified: repo <sha> (<date>) - env <name> <build> (<date>) -->
```

`overview.md` carries the repo half only. **The sha is read** per the repo-facts rule's *the commit* -
git, else its fallback - and from nowhere else. **Unreadable means the repo half is written
*unverified***, never guessed. **The date is the day the stamp
is written** - the session's date.

**Never invent the env half**: it cannot be derived from a repository. Carry forward what the file
already had, use what the user supplied, or omit the axis and say it is unstamped. An invented
environment is a false claim that outranks the truth for as long as nobody checks it.

## No-op when nothing changed

**A rebuild whose output is byte-identical to the file on disk writes nothing** and reports
`no-change`. Compare the rendered file to the file, never the sources to their last generation.
