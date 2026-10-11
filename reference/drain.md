---
doc: drain
title: The drain
---

## The drain

The harness memory store survives `/clear`, but nothing in it reaches a store, a projection or
another directory. The drain is what moves it into notes. Read that store, promote what passes an
admission test, and **clear every item whose content now has a home** - what this drain promoted,
what was already in the store, and what a registered repository's own files already hold (below). **A save that cannot drain says so**, and never
reports the drain done.

**Empty and unread are different answers, and only one of them is ever inferred.** *Empty* is a
listing that returned nothing; *unread* is everything else - the directory was not found, the glob
failed, a file would not parse. **Report `empty` only from a listing you actually got back**, and
otherwise say the store could not be read and name the path you tried.

**The drain never withholds the safe-to-clear line** - `/clear` ends the conversation and leaves the
memory store where it is. Report what is still only in memory, unplaced or unread, on its own line
above it.

**Clearing is bounded by what is filed, not by who wrote it or which run filed it.** An item whose
content lives in the store, or in a registered repository's files, is a second copy of something
already kept, and removing it is the same
trim the rest of the design performs - bounded by the content existing somewhere else. **An item
whose content has neither home is not cleared**, whatever it looks like and whoever appears to
have written it: the drain never removes something whose only copy it is.

### An item already in the store

**Already there is checked, then cleared - never skipped and kept.** Promotion skips a duplicate so
nothing is written twice; for a memory item that skip is where the work starts. Left in memory, the
item loads in every session and is read, routed and found again on every save. For each item
promotion finds already there:

1. **Route the entry it matched as if it were new** - its file, its scope, its area, per
   the promotion rule. The entry is judged, not the item.
2. **In the right place: clear the item.**
3. **In the wrong place: move the entry** - write it where routing sends it, in its own words, and
   remove it from where it was - **then clear the item.** Where the item says more than the entry,
   the extra is merged in the same write, per *Said elsewhere*.
4. **Report each one**: *already in `<file>` - cleared*, or *moved `<from>` → `<to>` - cleared*.

**Matching is by content, not wording.** An entry that carries the item's fact counts, whether it is
reworded or merged with others; one that only touches the same topic does not, and the item is
promoted as new. **Where you cannot say whether the store holds it, it does not**: the item stays,
and the report says so.

### A one-off goes to a work item, then is cleared

**A one-off fails admission to the registers, but it is not nothing** - *Worth recording* sends it to
the work item it came from. Kept in memory, it costs every session and every save, so the drain
files it in a work item's record and then clears it, like any other item now in the store:

1. **Already in a work item's record - cleared**, as *An item already in the store*.
2. **An existing work item it belongs to** - the one whose work it concluded, found in `index.md` by
   what the item names: an id, a title, the subject. **Append it to that item's `session.md`**, in
   today's `## session <date>` block, as `- from memory <file>: <the item's text>`.
3. **No work item fits - the one-offs item.** Append it there the same way. It is one standing work
   item per store, id **`memory-one-offs`**, found in `index.md` like any other; **mint it only
   where it does not exist yet**, per the bundle-shape rule - title
   *One-offs drained from harness memory*, `project` the projects whose items it holds (added to the
   list as more arrive), a `requirements.md` of one sentence saying it is where the drain files a
   one-off no other item fits, and a `session.md` that opens with `## open <date>` / *standing
   record: the drain files memory one-offs here*, so it is never reported stale.
4. **Then clear the item**, and report each one: *one-off → `<id>` - cleared*.

**Only what is history goes here.** An item that binds later work - a rule, a trap, a fact a session
would need - is not a one-off, whatever its wording: it is routed to its register as any other.

### An item already in the repository's own files

**Being in the repository's docs does not settle whether it goes in the store.** It was in memory
because a session needed it, and a session does not read every doc. So route it through promotion
like any other item, as if the doc did not exist; *Derivable when it is needed* is the test that
usually decides it, and its usual verdict is one line in the register it belongs to saying **when**
to reach for the file and what for. It may earn a full entry instead, or nothing.

**Then clear it, whatever promotion decided** - its content has a home either way. **The home is a
file in a repository this store registers, read in this run, that holds the item's content** - per
*Matching is by content*; one that only touches the topic, or could not be read, is no home. Report
each one: *in `<file>` - `<what promotion wrote, or nothing>` - cleared*.

**Clearing an item is two writes.** Remove the item's line from the store's `MEMORY.md`, then `Write` the item's file empty. If
the file cannot be emptied, put its line back: the index lists what is still in memory.
**`MEMORY.md` is the index, not an item** - the glob returns it with the items; never promote it,
and change only the lines of items you cleared. **An empty file is an item already cleared**: skip
it, and count it neither as an item nor as unread.

**This is one store, for one directory.** The harness keys memory by a slug derived from the working
directory, so the store a save can see belongs to the directory it ran in - not to the project, and
not to every project. **Name the store path in the report.** Where the repository's own slug and the
one in use differ - a save run from a subdirectory, or a project that has moved - a second store
holds items this run never saw, and saying so is what stops them being counted as drained.
`/nk:doctor` carries the finding.

**A session clears only its own directory's memory store.** Every other one is a sensitive file to
the harness: a write there asks each time and no setting pre-approves it, with nobody to answer it
is refused, and in auto mode each write is judged on its own. **So nothing writes another
directory's memory store**, by any tool or in any mode. Its items are cleared by the next save in
that directory once they are in the store - *An item already in the store* - with nothing to ask.

### Finding it

**`~/.claude/projects/<cwd-slug>/memory/`**, where `<cwd-slug>` is the **absolute working directory
with every character that is not a letter, digit, `_` or `-` replaced by `-`** - separators, the
drive colon and spaces all become `-`. `_` may be kept or replaced: **if no memory folder matches
and the path contains `_`, try again with `_` replaced as well.**
**Glob it; do not shell out for it.** `Glob` and `Read` reach that path on their own, and the
skills that read it are granted both.

**Glob `*/memory/*.md` with `path` `~/.claude/projects` and match the slug** (a `~` in the pattern
matches nothing) rather than composing the path blind:
the listing is what tells you whether the directory exists at all, and matching against it is also
how the sibling-store finding above is spotted.

**A relocated `CLAUDE_CONFIG_DIR` moves this and cannot be read from here.** Nothing in this
command's grant reads an environment variable, so where `~/.claude/projects/` does not exist,
**say the store could not be located and name the variable** - never that there was nothing in it.

**What cannot be placed is reported, never cleared.** One line per item, with where it would go if
you agree. **An instruction goes to the `instructions.md` of the scope it holds at** - the item, an
area, the project, the workspace - and an instruction that holds everywhere goes to global's,
`~/.notekeeping/instructions.md`, which every session loads through the global rule file. **Never write your harness
instructions file**: an instruction is filed in a store or not at all.
