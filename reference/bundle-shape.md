---
doc:   bundle-shape
title: Minting a work item - its id, its folder, and what makes it a bundle
---

# A work item's shape

> **Two commands mint, and they mint the same thing.** `/nk:work` opens the space up front;
> `/nk:save` mints when it has nowhere to write. Each owns **when**. This file owns **what**, and
> both follow it.

## The id

**`--id` when it was given, otherwise the next store counter.**

**The counter is derived, never stored.** Take the highest numeric `id` in `index.md`. Where there is
no `index.md`, scan every `work/<bucket>/` bucket and take the leading digits of each counter folder
name (`<id>-<slug>`) - a folder name is never bare digits, because a counter id always takes a slug.
Add one, **pad to four digits**. Nothing persists, so nothing can drift, and rebuilding the index from
the folders on disk recovers it exactly. **Numbers are never reused**: nothing here is ever deleted.

**The padding is load-bearing, not cosmetic.** `0001` sorts before `0010`; `1` does not sort
before `10`.

**If the minted id would match this store's `tracker_id_pattern`, refuse and name the collision.**
A bare-numeric tracker pattern would make the minted id look like a ticket key, and the next read
goes looking for a ticket that never existed. Say which setting collides rather than falling back
silently.

**An explicit `--id` is checked before it mints.** Matching `tracker_id_pattern` makes it a tracker
key, not a local id, and it mints as given. Otherwise it is a local id and is checked against
`local_id_pattern` (the config defaults); a mismatch asks, naming
the pattern - refused instead under `--oneline`.

**The folder never renames, and ids are opaque and stable.** A tracker id is attached later with
`/nk:work --also`. Start with a counter; add the ticket when it exists.

## The folder

`work/<bucket>/<id>/`, where the bucket is **the period of first work**, as `work_bucket` names it
(the config defaults, default `month`):

| `work_bucket` | Folder name |
|---|---|
| `month` | `YYYY-MM` |
| `quarter` | `YYYY-Qn` |
| `year` | `YYYY` |

**The setting is read at mint time, and only then.** Changing it later never moves an existing
item's folder, and every command that finds items accepts any bucket folder -
`work/<bucket>/<item>/` - so a store carrying both shapes is found in full either way.

**A meaningless id takes a slug - `<id>-<slug>` - and nothing else does.** Meaningless means it
matches `tracker_id_pattern` or `^\d+$`; a tracker key and a counter both do, so both take a slug,
derived from the title. Anything else is a name the user chose, which already says what it is.

**The test is on the id itself, never on how it arrived**: `TKT-482` takes a slug, `spike-auth`
does not.

## `requirements.md` is what makes it a bundle

**Write it, or do not create the folder.** A folder without one fails `/nk:doctor` on the day it is
made, and leaves the next `/nk:load` nothing to resume from.

**Thin is fine; empty is not.** Write what is thin, in a sentence, per the definition resolved
through the file-definition rule. **Never write an empty heading.** If
there is genuinely nothing to write, say what is unknown and why.

## Resolving the project

**Never invent one.** Resolve the working directory to a project per the store-resolution rule's
*Resolving a project inside the store* - the folder's read line, then the registry's `dirs:`; no git is run to find a
repo root. **Never fall back to a directory's basename.**

**If it does not resolve, ask** - **except under `--oneline`, where the question comes back as a
refusal naming `--project <name>`**
(the consumer contract). A guessed project routes promotion to the wrong scope and reads the state of a repository
the work has nothing to do with.

**`--project <name>` supplies the answer**, and is what a refusal names. The name must already be
registered; it selects a project and never creates one.

## After minting

**Say plainly that you created rather than resumed**, and where the folder is.

**Regenerate the store's `index.md`** per the index shape.

## Never two folders for one piece of work

**Resolve against `index.md` before minting, not against your memory of the session.** Two saves an
hour apart, with the session drifted, can infer two slugs for one piece of work and open two
folders - which nothing later merges, because nothing here is ever deleted.

**An id that already exists is never a silent second folder.** `/nk:work` redirects to
`/nk:load <id>`; `/nk:save` resumes it as the target.
