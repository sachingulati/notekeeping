---
doc:   bundle-shape
title: Minting a work item - its id, its folder, and what makes it a bundle
---

# A work item's shape

> **Two commands mint, and they mint the same thing.** `/nk:work` opens the space up front;
> `/nk:save` mints when it has nowhere to write. Each owns **when**. This file owns **what**, and
> both follow it.

Stated once, because two writers that state it separately drift - the same reason
`${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md` is stated once and cited by its six callers, five of them writers.

## The id

**`--id` when it was given, otherwise the next store counter.**

**The counter is derived, never stored.** Scan every `work/<YYYY-MM>/` bucket for ids matching
`^\d+$`, take the highest, add one, **pad to four digits**. Nothing persists, so nothing can drift,
and rebuilding the index from the folders on disk recovers it exactly. **Numbers are never reused**:
nothing here is ever deleted.

**The padding is load-bearing, not cosmetic.** `0001` sorts before `0010`; `1` does not sort
before `10`.

**If the minted id would match this store's `tracker_id_pattern`, refuse and name the collision.**
A bare-numeric tracker pattern would make the minted id look like a ticket key, and the next read
goes looking for a ticket that never existed. Say which setting collides rather than falling back
silently.

**The folder never renames, and ids are opaque and stable.** A tracker id is attached later with
`/nk:work --also`. Start with a counter; add the ticket when it exists.

## The folder

`work/<YYYY-MM>/<id>/`, where the bucket is **the month of first work**.

**An opaque id takes a slug - `<id>-<slug>` - and nothing else does.** Opaque means it matches
`tracker_id_pattern` or `^\d+$`; a tracker key and a counter both do, so both take a slug, derived
from the title. Anything else is a name the user chose, which already says what it is.

**The test is on the id itself, never on how it arrived**: `TKT-482` takes a slug, `spike-auth`
does not.

## `requirements.md` is what makes it a bundle

**Write it, or do not create the folder.** A folder without one fails `/nk:doctor`'s first error
check on the day it is made, and leaves the next `/nk:load` nothing to resume from.

**Thin is fine; empty is not.** Write what is thin, in a sentence, per the definition resolved
through `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`. **Never write an empty heading.** If
there is genuinely nothing to write, say what is unknown and why.

## Resolving the project

**Never invent one.** Resolve the working directory to a repo root and match that root against the
projects registered in the store, per
`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. **Never fall back to a directory's basename.**

**If it does not resolve and you cannot ask, write `project: []`** and name it as unresolved in the
body. A guessed project routes promotion to the wrong scope and reports git state for a repository
the work has nothing to do with.

**`--project <name>` supplies the answer when nobody can be asked**, and is what a refusal names.
The name must already be registered; it selects a project and never creates one.

## After minting

**Say plainly that you created rather than resumed**, and where the folder is.

**Regenerate the store's `index.md`** per `${CLAUDE_PLUGIN_ROOT}/reference/index-shape.md`. A mint
is one of the few things that genuinely changes that file, and the new row is the point of the step.

## Never two folders for one piece of work

**Resolve against `index.md` before minting, not against your memory of the session.** Two saves an
hour apart, with the session drifted, can infer two slugs for one piece of work and open two
folders - which nothing later merges, because nothing here is ever deleted.

**An id that already exists is never a silent second folder.** `/nk:work` redirects to
`/nk:load <id>`; `/nk:save` resumes it as the target.
