---
doc:   store-writes
title: What a skill may write, and where
---

### The writes that land outside a store

**The property this protects is not *never outside a store* - it is *only into a target somebody had
to name*.**

**None of them is committed, and none is read by anybody but you.** Three are files the plugin
writes and owns; two are entries added to files of yours (`.git/info/exclude`, `~/.claude/settings.json`); one removes lines only from a context file in no work tree, or an ignored one inside a work tree, so
even there nothing a teammate can see is touched; the drain is below the table, because what bounds it is not a path.
**All but the trim and the drain happen at registration, or in a repair** - never on a save.

| What | Target, and what bounds it | Written by |
|---|---|---|
| the repo read line | `<repo>/CLAUDE.local.md` - only a repository **registered as a project**, at a folder that still exists | `init` at registration; `doctor --fix` |
| the workspace read line | `<workspace-root>/CLAUDE.local.md` - only the directory holding that store's `.notekeeping/` | `init` when it creates the store; `doctor --fix` |
| the global rule file | `~/.claude/rules/notekeeping.md` - plugin-owned whole | `init` when it creates global; `doctor --fix` |
| the ignore step | `<repo>/.git/info/exclude` - never `.gitignore`, and written **before** the read line | the repo read line's writers |
| the read permission | `~/.claude/settings.json` - **only adding** the entries below | `init`, and `doctor`'s repair |
| idle compaction off | `~/.claude/settings.json` - **only adding** `"idleCompaction": false`, and only on a yes | `init`, when it creates a store |
| the context-file trim | a `CLAUDE.md` or `CLAUDE.local.md` - **only one in no work tree, or ignored inside one; only lines whose content is in the store; the file copied whole into the run's `copies/` first**, and only on the yes to a proposal that showed every line | `adopt` |

**The trim is one of two writes that remove rather than generate - the drain is the other -**
which is why it is bounded twice
over: by where the file is, and by the content already existing somewhere else.
The adopt command's trim rule holds the invariant.

### The drain

**The drain clears items from one harness memory store: the one keyed by this session's own working
directory**, `~/.claude/projects/<slug>/memory/`. Clearing an item is its line removed from that
store's `MEMORY.md` and its file written empty - nothing is deleted. **An item is cleared only once
its content is in the store, or in a registered repository's files read in that run**; one with
neither home stays, and the report names it. No other directory's memory store is written, by any
command. `save` drains, and `adopt` clears the same store as one of its trims, copying its files into
the run's `copies/` first - the drain rule holds the rest.

**No read line is written by `save`, `project`, `adopt` or `upgrade`** - the read line does not change
when the notes do. The projections rule holds what each line says; the projection-writing rule, how it is written without clobbering.

### The read permission

**Without it, every read of the notes is refused** - so creating a store adds it. Every read line
points above the repository by absolute path, and Claude Code reads outside the working directory
only where `permissions.additionalDirectories` allows it; missing, the line arrives and the read it
asks for fails.

**Add, never replace.** Read `~/.claude/settings.json`, parse it, and add each missing path to
`permissions.additionalDirectories` - creating the key, or the file, only if absent. Every other key,
every other entry and the order of what is there stay exactly as they were. **Write absolute paths**,
forward slashes, the same form the projections use.

The same add-never-replace rule also covers three more entries: `Skill(nk:*)` in `permissions.allow`; the plugin's version-independent folder, `<plugin cache>/<marketplace>/nk`, in `permissions.additionalDirectories`; and `Edit(//<absolute store path>/**)` in `permissions.allow`, one per store. **The `//` prefix is required on every platform**: in user settings a single leading `/` anchors the rule at `~/.claude/`, not at the filesystem root. The absolute path follows the `//` without its own leading slash - `//abs/path/to/ws/.notekeeping/**`, with the drive letter first on Windows, `//c/...`.

**If the file does not parse, write nothing to it.** Report that, and print the one line to add by
hand - a settings file the plugin cannot read is one it must not rewrite.

**Only the user's own settings, never a project's.** `.claude/settings.json` in a repository is
committed and the team's; this entry is personal, like every other write on this list.

### Idle compaction

**Claude Code compacts a long session left idle** - past its context threshold, about an hour
after the last request, shortly before the prompt cache expires - and a save after that records only
what the summary kept. `idleCompaction` turns it off; it is on by default.

**Asked, never set.** A run that creates a store, where `~/.claude/settings.json` does not set
`idleCompaction`, asks once: turn it off, or keep it. The question says what each costs - on, a long
session you step away from is summarised before you save it; off, the first message after a long
break sends the whole conversation uncached - and that `/config` changes it at any time. **A key
already present, either value, is the user's answer**: it is not asked about and never changed.

**On a yes, add `"idleCompaction": false` at the top level**, by the same rules as the read
permission: add, never replace; if the file does not parse, write nothing and print the line to add
by hand. On *Keep it*, nothing is written.

### The one thing that can leave this machine

**A report can be published as a page** - the report-page rule. It is
not on the table above, and it is named here because it is the only thing the plugin does that
leaves the machine at all.

**Four properties bound it, and they are the reason it is allowed:** it happens **only on an
explicit pick** in the run that offers it; it carries **only what that run already printed** to the
terminal; **it never writes store content** - where publishing fails, the fallback is one local
file, the run item's `report.html`, bookkeeping rather than knowledge, exactly as the
run's report is; and **the terminal report stands whether it happens or not**, so nothing depends on
it. **A page published without being asked for is a defect**, exactly as a write outside a store is.

**Nothing else, ever. A write outside a store that is not on this list is a defect, not a judgment
call.** What each read line and the global rule file may contain is governed by
the projection rule, and the trim's bound is
the adopt command's; this table settles only *which targets exist*, so
that *inside a store* stays checkable rather than approximately true.

## Every run

**Nothing changed, nothing written.** A command whose output would be byte-identical to what is on
disk writes nothing and says so - *nothing new since the last save*. Compare the rendered file to the
file, never the sources to their last generation. A second `/nk:save` straight after the first leaves
`resume.md` and `session.md` untouched: an empty diff and a fresh date that say nothing new are
churn, for a person reading git history as much as for a tool calling repeatedly.

**Check before touching the filesystem, in one order: the flags, then the store, then the project.**
Where a run cannot go on, **which check fires first decides what the user fixes first** - so the
cheapest, most actionable one goes first: a flag the user controls, before a missing store, before
an unresolvable project.

## Store-wide write rules

- **Never delete** - retiring is marking superseded. Two exceptions: a credential the user asks to
  forget, which `secrets.md`'s definition deletes with its entry, and a source a release's gather step
  has converted, which only `/nk:upgrade` removes, once it is copied into that run (the file-facts
  rule, *move a converted source*).
- **No stubs** - a target with no admitted entry is not created, and an empty heading is never written.
- **Nothing changed, nothing written** - compare the rendered file to the file; this is the *Every run* paragraph above, not restated here.
