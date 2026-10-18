# Adopt - harness memory

**Read this when the inventory holds a harness memory store, or a report being applied holds a memory
trim.** The harness keeps one memory store per working directory, `~/.claude/projects/<slug>/memory/`, and
loads it in every session there. A save drains only the one its session can see
(the drain rule's *This is one store, for one directory*), so what a store
left by a subdirectory, or by a project that moved, holds reaches the notes through nothing else.
**The bare form reads them all**, within the limit like any other source, places their items, and
clears only its own directory's store (*Clearing is the trim*, below).

**Finding them** is the drain's *Finding it*: glob `*/memory/*.md` with `path` `~/.claude/projects`.
`MEMORY.md` is the index, never an item, and an empty file is an item already cleared - neither is
read or counted. Each memory store is a root in the narrowing question, with its item count and size.

**Which directory a memory store belongs to.** Compute the slug of every directory this store
registers - each project's `dirs:` and the workspace root - and match. An exact match is that
directory. A slug that extends one by `-<rest>` may be a subdirectory or a sibling -
`…-apps-web` is `apps/web` or a folder named
`apps-web` - and **the candidate path that exists decides**; a check of named
paths, not a scan. Both existing: unplaced, and reported. Neither: the path is gone, and the
moved-project tests below apply.

**A memory store whose path no longer exists is tried as a moved project.** No registered directory
matches a path that is gone, so try these in order, and stop at the first that answers:

1. **The store says so** - an entry in this store that names the old path, with either separator,
   as moved, and says where to.
2. **The name** - the slug's tail on `-` boundaries, longest first, against each registered
   project's name and each of its `dirs:` folder names, ignoring case: `…-src-billing` and a
   project `Billing`. **Exactly one project may match**; two or more is no match, and the report
   names them.

**A match is an inference, never a measurement.** The narrowing question shows it as *moved?
`<old path>` → `<project>`*, with the test that found it, so it can be dropped before anything is
read; the report repeats it beside every item placed through it. With no match, the memory store is
unheld, as below, and the report names what it was tried by.

**Its reach is its directory's.** A memory store loads only in sessions under its directory, so its
items go no wider than the project or workspace holding it - the reach rule in `sources.md`'s *Context files*. **A memory store
whose directory this store does not hold** - another workspace's, or a moved project neither test above
matched - **is reported unheld, with the directory its slug names and its item count, and is neither read nor
touched** - its items could not be placed here, so reading them would only spend the limit: the next step is `/nk:adopt` from the store that holds it, or `/nk:init` to register it.

**An item is placed by the drain's rules**: promotion per the promotion rule,
then `drain.md`'s *An item already in the store* - its entry's place checked, and moved where it is
wrong - *A one-off goes to a work item*, a mint per the bundle-shape rule, and *An item already in the repository's own files* - judged
for the store as if the file did not exist. These writes are the store half of the report, in the
categories of the files they change.

**Clearing is the trim, and it reaches one memory store: this session's own directory's** - the only
one a session writes, per the drain's *A session clears only its own directory's memory store*. It is
one trim category - *trim · memory `<slug>`* - its items listed with the store file or work item each
went to, and covered by *Apply and trim* or *Trim only* like any trim, per `trim.md`. **Before its first item is
cleared, the memory store's files are copied** into the run's `copies/memory/<slug>/`,
`MEMORY.md` included, and clearing is the drain's two writes. A memory store is in no work tree, so it
is always trimmable.

**Every other memory store is placed and left.** Its items are in the store once the apply has
written them, so the next `/nk:save` in its directory clears them as already there. **A memory store
whose directory is gone loads in no session**, so leaving it costs no context: the report names its
path as safe to delete by hand, and nothing here deletes it.

**A memory item whose content is in a registered repository's file is cleared too** - the file read
in this run and named in the item's trim line, per that drain section.
**An item not placed, one that could not be read, and a credential are never cleared** -
each stays where it is, named in the report's part 4.
