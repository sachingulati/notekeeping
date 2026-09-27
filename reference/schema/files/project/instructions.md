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
env_axis:    optional
---

## Question
What must I *do* here, every time, whether or not I would have thought of it?

## Admission
You asked for this to be standing behaviour, and it holds for the whole project rather than for one
task. **An instruction is obeyed, not looked up** - which is why it does not live in a register and
carries no correction shape. A register entry is a claim you could be wrong about; this is not that.

## Exclusion
A fact that is true -> `NOTES.md`, or the register it belongs to. True of one task -> that work
item's `instructions.md`. **Conditional on a topic** -> that topic's area, once the area exists -
an entry that begins *when the work touches X* belongs to X. True of every project in the workspace ->
the workspace's `instructions.md`; true of every project you work on -> the global one. **Team-wide -> the repository's committed `CLAUDE.md`**, which
this plugin never writes.

## Entry format
```markdown
- **Do X, not Y.** The condition under which it applies.
  **Retire when:** <the observable that ends it>. `(source - date)`
```

**The second line matches `decisions.md`'s exactly**, because the two are the same construct: a
required trigger, written inline, checked by one `doctor` finding. `Would reopen if:` and
`Retire when:` are the only two trigger tokens in the design, and keeping their shape identical is
what lets one check read both.

**Imperative first**, the same reason a register puts the claim first: what is scanned here is what
to do. Provenance on every entry, as everywhere else.

**`Retire when:` is required**, and it is what stops this file accreting. `/nk:doctor` reports an
entry whose trigger has plausibly fired, exactly as it does for a `decisions.md` entry's
`Would reopen if:`. **An instruction nobody can imagine retiring is a fact in the wrong file.**

## How one is found

**The filename is the marker, and nothing is stamped onto an entry.** `instructions.md` sits at a
path the schema fixes, so `<store>/**/instructions.md` already finds every instruction in a store - the same argument that keeps a per-entry token out of `gotchas.md`. A token
here would also fight the entry format's own rule: what leads an entry is the imperative, not a
field name.

**Two tokens are greppable and are meant to be.** `**Retire when:**` finds every trigger, in this
file and in `decisions.md` alike; `## Standing instructions` finds the rendered block in a
projection, and detection keys on that heading exactly as the no-clobber rule keys on
`notes:begin`. **Both are checked by grep, never by reading the file** - the idiom `/nk:doctor`
already uses for stamps.

## This file is delivered, which is what the budget is for

It is projected into the always-loaded block under `## Standing instructions` -
`${CLAUDE_PLUGIN_ROOT}/reference/projections.md`. Every entry is therefore charged **in every prompt
in that repository**, against the same ceiling the facts are competing for. **An instruction that
crowds out a fact is a trade, and this budget is what makes it a visible one.**

**A store path inside an entry is written absolute on render** - the same rule, and the same reason,
as the `Read on demand` line. The projection sits in the repository and the store sits above it, so
a store-relative path resolves to nothing from where it is read. You author it short; the projection
writes it in full.

**Conditional loading is what this file is for.** *When the work touches X, read `<area>` first* is
an instruction, not a pointer: the catalogue lists what exists, and an entry here is what makes one
of those shelves load at the moment it matters. Name the cues that identify the condition - the read
is reliable once the condition is recognised, and recognising it is the part that is judgement.
