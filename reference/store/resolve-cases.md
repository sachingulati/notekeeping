---
doc:   store-resolve-cases
title: Resolving a store - the cases the common path names
---

# Resolving a store - the cases

**Read a case when the store-resolution rule names it, not before.** Each heading here is the name
that rule uses.

## Probing by name

**A store is a hidden directory, and a listing is not a reliable witness to one:** a plain `ls` omits
it, and a glob of `*` has both skipped hidden directories and descended into them, depending on the
harness. A walk built on a listing can report *no store* while standing next to one.

**The failure is silent, and so is the obvious fix.** Probe for the store **by name**; never ask a
listing to reveal it:

| Pattern | Finds a `.notekeeping/`? |
|---|---|
| `<dir>/.notekeeping/*` | **yes** - the literal name is in the pattern. **Use this one** |
| `<dir>/**/<file>` | **yes** - a recursive descent crosses into hidden directories |
| `<dir>/*` | **not dependably.** It has behaved both ways - and where it descends, it returns files from any depth, so a hit does not say which level holds the store |
| `<dir>/.*` | **no.** The intuitive correction matches hidden files at that level, not what is inside a hidden directory, and reports nothing rather than erroring |
| `<dir>/*/<file>` | **no - it can return nothing with the file present.** See the store-walk rule's *A pattern that matches nothing* |

**A walk that returns "no store" after only listing directories has not looked**, and must not be
reported as an absence.

**Walking up is resolution, not discovery, and the difference is what you match on.** You are
looking for one exact name that the plugin creates and nothing else does. A `.notekeeping/` exists
only because someone ran `/nk:init` there, so finding one above you is finding a declaration, not
guessing from shape.

## Knowing that other stores exist

**Global's `config.md` carries a `## Workspaces` registry** - the absolute path of every workspace
root whose store `/nk:init` has created on this machine:

```
## Workspaces
- /abs/path/to/ws
```

A store config's own `## Projects` is the same shape, one line per registered project:

```
## Projects
- **repo-a** - dirs: /abs/path/to/ws/repo-a
```

Both are written at creation, for the same reason, and neither is **a setting**.

**It is for enumeration, and never for resolution.** *Which store am I in* is always the walk, on
every command, with no exception and no shortcut. The registry answers only *what else is there* -
naming a candidate a command may then offer to act on.

**A registry entry is a candidate, never a fact.** Before acting on one, prove the store is there the
way existence is always proved here: probe `<path>/.notekeeping/*` and take a hit. A directory can be
moved, deleted, renamed or sit on a volume that is not mounted, and **a path written down a year ago
is not evidence that anything is there now.**

**A stale entry is reported, never removed.** `/nk:doctor` names it; nothing deletes it, because an
absent path is as likely to be an unmounted disk as a deleted store, and the two are not
distinguishable from here.

## A read line that is not followed

A project line whose `<store>` is not the store resolved, or whose `projects/<name>/` that store does
not hold, is not followed. Say why:

| The line | Say |
|---|---|
| names another store | this folder reads the notes of a store it is not inside - name both. It was moved out of that workspace; `/nk:init` here, or moving it back, settles which it belongs to |
| names a project this store does not hold | the line is broken - name the line and `/nk:doctor` |

## Two project lines on the path

A block in a subfolder as well as at the repository root is resolved by the nearer, and the session
is reading both projects' notes, because the harness loads every `CLAUDE.local.md` from the working
directory up. Say so, naming both files.

## Reaching a project from outside its folder

**What `dirs:` is still for.** A folder that resolves by its read line needs no registry entry to be
worked in, but a project you are not standing in - a dependency's branch, a read line `/nk:doctor`
checks, a repository `/nk:project` reports on - is reachable only through its registered path.
Finding it any other way would mean scanning.

## A folder the registry has lost

**Renaming or moving a repository inside its workspace leaves its `dirs:` entry behind.** The read
line moved with the folder, so the read line still resolves it; the registry still names the old
path. When the read line resolves the project and the working directory is under none of the store's
`dirs:` entries, probe that project's entry - `Read` `<dir>/.git/HEAD`, then `<dir>/.git`:

| The registered directory | Say |
|---|---|
| **holds no `.git`** | one line: *`<name>`'s registered folder `<dir>` is gone, and this folder carries its read line - `/nk:init` here updates it.* |
| **still holds one** | nothing - a second folder for one project is not handled |

**It is information, not a question**, so it repeats until `/nk:init` runs and asks nothing.
**Nothing is written**: an absent path is as likely to be an unmounted volume as a renamed folder,
and updating the registry is `/nk:init`'s.
