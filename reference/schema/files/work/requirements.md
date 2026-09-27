---
file:        requirements.md
scope:       work
schema:      1
enabled:     true
tier:        core
shape:       document+append
owner:       nk:work
trigger:     always - the bundle is this file
authority:   original
budget:      none
env_axis:    optional
---

## Question
What was asked, and why?

## Admission
The ask itself, its rationale, its acceptance criteria, and its boundaries - what is explicitly out
of scope.

## Exclusion
How you will do it -> plan.md. What happened, and what you learned on the way -> session.md.

## Source
When the body was written from material the user supplied or a tracker issue rather than from
inference, **cite it**: the path exactly as given or the issue key, and the date it was read. One
line, at the end of the body, above any amendment.

**Distil, never transcribe.** A document that lives elsewhere stays there - this file holds the ask,
the rationale, the acceptance criteria and the boundaries, not a second copy that nothing keeps in
sync. **Every statement traces to the source and nothing is added:** where the source is silent, say
so under the heading it belongs to rather than writing a plausible criterion. A fabricated
acceptance criterion is worse here than in any other file, because `authority: original` is what
this one claims.

## Frontmatter
These fields, in this order, with no gaps. Omit an optional field entirely rather than writing it
empty.

| Field | Required | Value |
|---|---|---|
| `id` | yes | the primary id. Opaque, unique, directory-safe, stable |
| `ids` | no | a list, when several tracker ids are **one piece of work** sharing this requirement |
| `title` | yes | one line, what it is |
| `type` | no | free text - `bug`, `story`, `task`, whatever your tracker calls it |
| `project` | yes, may be empty | **singular key, list value** - `project: [web-client]` |
| `parent` | no | the id of a containing work item |
| `tags` | no | **a list.** Free-text labels this item shares with others - a recurring effort, a theme, a push |
| `created` | yes | date |

**`tags` is plural on both sides, and that is deliberate.** The frontmatter key is `tags:` and the
resolver's column is `tags` - **the same word**, unlike the `project:`/`projects` asymmetry below,
which is a documented trap and is not reproduced here.

**A tag is a flat label, not a relation.** It has no owner, no requirement of its own and no
lifecycle - it is a name several items happen to share. An item may carry any number of them, and
two items sharing one are not otherwise connected.

**No `updated` field.** Last activity is **derived, never stored** - the newest dated block in
`session.md`, which is append-only and therefore cannot drift, falling back to the `work/<YYYY-MM>/`
bucket for an item too new to have one
(`${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md`). A stored date would have to be
maintained by every writer, and a field that nothing maintains is worse than no field: it reads as
authoritative while being wrong.

**`project` is singular and its value is a list.** The resolver's column is `projects`, plural, and
holds this list verbatim - the two names are different on purpose and must not be swapped. Every
command that reports git state reads `project:`, so writing `projects:` here silently breaks them.

**Never invent a project**, and never fall back to a directory's basename. How it resolves, and
what to write when it does not, is
`${CLAUDE_PLUGIN_ROOT}/reference/bundle-shape.md`.

**No status field, and the resolver carries no `status` column either.** A work item holds no
workflow state - see the trigger note above. Where the session reaches the tracker, `/nk:load` reads
live status at read time; it is never cached in the store, because cached live state carries an
authoritative look while going stale the moment somebody *else* acts.

## Entry format
The body is written once and is never rewritten by any command - that is what makes this file
evidence of what was originally asked. Every later change is an appended, dated amendment below a
rule.

Two amendment kinds, because two different things change:

```markdown
---
## Amendment - <date> - scope changed
**Asked by**    who asked, and where
**Change**      what is different now
**Because**     why
**Invalidates** the files or sections this silently made wrong

---
## Amendment - <date> - understanding corrected
**I thought**   the belief you held
**Actually**    what is true
**Found via**   what showed you
**Invalidates** the files or sections this silently made wrong
```

`Invalidates` is required in both forms. It is the only mechanical link between a change and the
files that quietly became wrong because of it, and doctor reads it.

## Read modes
`active` resolves the chain to current and is the default. `latest` returns the most recent
amendment alone. `full_history` returns the body plus every amendment in order - that mode is what
makes this file evidence.
