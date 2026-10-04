---
file:        instructions.md
scope:       project
schema:      1
enabled:     true
tier:        core
shape:       document+append
owner:       promotion
trigger:     always
authority:   original
budget:      2000 bytes
---

## Question
What must I *do* here, every time, whether or not I would have thought of it?

## Admission
The user asked for this to be standing behaviour, and it holds for the whole project rather than
for one task. **An instruction is obeyed, not looked up** - which is why it does not live in a
register and carries no correction shape. A register entry is a claim you could be wrong about; this
is not that.

## Exclusion
A fact that is true -> `NOTES.md`, or the register it belongs to. True of one task -> that work
item's `instructions.md`. **Conditional on a topic** -> that topic's area, once the area exists -
an entry that begins *when the work touches X* belongs to X. True of every project in the workspace ->
the workspace's `instructions.md`; true of every project you work on -> the global one. **Team-wide ->
the repository's committed `CLAUDE.md`**, which the plugin never edits.

## Entry format
```markdown
- **Do X, not Y.** The condition under which it applies. `(source - date)`
- **Do X, not Y.** The condition under which it applies.
  **Retire when:** <the observable that ends it>. `(source - date)`
```

**Imperative first**, the same reason a register puts the claim first: what is scanned here is what
to do. Provenance on every entry, as everywhere else.

**`Retire when:` is optional - written only when the instruction has an end.** The user or the
source states one, or the instruction is plainly temporary: *until the migration lands*, *for now*,
*while the freeze holds*. **A standing convention has none** - *use conventional commit messages*
ends when the user changes their mind, and a trigger saying so is noise. **Never invent one, and
never write a placeholder**: with no end stated, the entry is one line. `/nk:doctor` reports an
entry whose trigger has plausibly fired, exactly as it does for a `decisions.md` entry's
`Would reopen if:`; an entry with no trigger is never a finding. **What stops this file accreting is
its budget** - every byte is charged in every prompt - and `/nk:review`, which proposes what to prune.

## How one is found

**The filename is the marker, and nothing is stamped onto an entry.** `instructions.md` sits at a
path the schema fixes, so `<store>/**/instructions.md` already finds every instruction in a store.

**One token is greppable and is meant to be.** `**Retire when:**` finds every trigger in this
file, as `**Would reopen if:**` does in `decisions.md`. **It is checked by grep, never by reading the file.**

## Delivery

`NOTES.md` reads on to this file, so it is read before any task here. Every entry is charged **in
every prompt in that repository**, against its own 2000-byte ceiling. An area is reached through
`areas/INDEX.md` and `NOTES.md`'s area lines, never through an entry here.
