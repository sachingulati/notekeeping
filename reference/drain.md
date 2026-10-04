---
doc: drain
title: The drain
---

## The drain

The harness memory store survives `/clear`, but nothing in it reaches a store, a projection or
another directory. The drain is what moves it into notes. Read that store, promote what passes an
admission test, and **clear what was promoted**. **A save that cannot drain says so**, and never
reports the drain done.

**Empty and unread are different answers, and only one of them is ever inferred.** *Empty* is a
listing that returned nothing; *unread* is everything else - the directory was not found, the glob
failed, a file would not parse. **Report `empty` only from a listing you actually got back**, and
otherwise say the store could not be read and name the path you tried.

**The drain never withholds the safe-to-clear line** - `/clear` ends the conversation and leaves the
memory store where it is. Report what is still only in memory, unplaced or unread, on its own line
above it.

**Clearing is bounded by what was filed, not by who wrote it.** An item whose content now lives in
the store is a second copy of something already kept, and removing it is the same trim the rest of
the design performs - bounded by the content existing somewhere else. **An item that was not
promoted is not cleared**, whatever it looks like and whoever appears to have written it: the drain
never removes something whose only copy it is.

**Clearing an item is two writes, never a deletion** - nothing the plugin is granted removes a
file. Remove the item's line from the store's `MEMORY.md`, then `Write` the item's file empty. If
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
