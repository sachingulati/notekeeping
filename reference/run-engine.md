---
doc:   run-engine
title: The run engine - one work item per run of adopt, doctor, review and upgrade
---

# Runs

**Four commands report a list and have something to apply - `/nk:adopt`, `/nk:doctor`, `/nk:review`
and `/nk:upgrade` - and every one of them runs on this engine.** The engine owns the phases, the run
item and its files, resuming, and the decisions one run carries into the next. A command supplies
three things only: **what it inventories**, **its finding kinds** - each with its fingerprint, its
apply action, and whether it writes outside the store - and **its checks**. The list itself is the
report-shape rule; the question, the answers and the apply are the report-apply rule.

## The run item

**A run is a work item**, minted by the bundle shape like any other - the store counter, a slug from
the title, the `work_bucket` folder - and found the way any item is found: by its id, its title, or
its tag.

| | |
|---|---|
| **Tag** | `run-<command>` - `run-adopt`, `run-doctor`, `run-review`, `run-upgrade`. Reserved: no other item carries a `run-` tag |
| **Title** | what ran, on what - *Doctor run on the platform workspace and global*, *Adopt run on ~/notes, docs/ only*. Written at the start and never changed: the outcome lives in `summary.md` and `resume.md` |
| **`project:`** | the project the run was scoped to, or empty for a run over a whole store |
| **Where** | the workspace store the run resolved - one run item per store a run reports on, as `/nk:upgrade` does for each store it migrates |

**It is minted at the first thing the run keeps** - its first draft, or a report with at least one
item - and not before. **A clean run mints nothing**: its reply says it found nothing, names how many
decisions earlier runs still carry, and leaves them as they were. Minting regenerates `index.md`, per
the index-writing rule.

**`requirements.md` is the run's ask**, written once at the mint, per its definition: the command as
typed, the arguments as resolved, the scope - stores and projects - and the run it continues, or
none.

### Its files

| File | Holds | Written |
|---|---|---|
| `requirements.md` | the ask - above | at the mint |
| `inventory.md` | what the run looked at, one row per source or subject - per its definition | at the mint, then as each source is read or drafted |
| `report.md` | the numbered items, **each with its own state and answer** - per its definition | at the end of the pass; states and answers as they change |
| `log.md` | **append-only**: every write and every trim, before and after - per its definition | during an apply |
| `drafts/` | one file per subagent batch, above the threshold - *Drafts*, below | during the pass |
| `hash/` | one file per item whose fingerprint hashes a text, holding exactly that text - what the hash was taken of | at the report |
| `copies/` | a byte-exact copy of every file outside the store the run is about to change, before the change | during an apply |
| `summary.md` | what the run did, in plain words - per its definition | at the end of every run |
| `resume.md` | where the run stands and what is next - per its definition | at the end of every run |

**A run item has no `session.md`.** `log.md` is its record, and `report.md`'s states are its
position.

**Nothing in a run item is ever deleted**, `copies/` above all: a copy is the only way back from a
write outside the store. A user may delete a run item by hand; no command does.

## The phases

**Seven, in order, and every command runs all of them** - a phase with nothing to do for a command
says so and passes.

| Phase | Does | Writes |
|---|---|---|
| **1 · Scope** | resolve the stores, the arguments and what the run continues; ask the command's narrowing question, if it has one | nothing |
| **2 · Inventory** | list what this command reads - files, sizes, checks - before any content is read; drop what an earlier run already read and that has not changed | nothing, until the mint |
| **3 · Pass** | read and judge: one pass under `one_pass_max_bytes`, drafts above it | drafts, above the threshold |
| **4 · Report** | number the items, look up each fingerprint in earlier runs, write `report.md` | the run item, if it has an item |
| **5 · Ask** | the command's question, per the report-apply rule | answers |
| **6 · Apply** | the approved writes, each logged before and after | the store; outside it only what the command's kinds allow |
| **7 · Record** | `inventory.md` brought up to date, then `summary.md` and `resume.md` | the run item |

**Phase 7 runs at the end of every run that has an item** - after the report when nothing is
applied, after the apply when something is, and again after a later `--apply` or re-entry.

## The pass, and drafts

**The inventory's total size decides how the pass reads**, against `one_pass_max_bytes` (the config
defaults, 700 KB). It is not a limit on the run - each command keeps its own, `adopt_max_bytes` for
adopt.

**Under it, one pass**: the run reads every source itself, judges, merges and writes the report at
the end. No drafts.

**Above it, drafts.** Sources go to subagents in batches - never more running at once than
`max_parallel_subagents`, per the store-resolution rule. **Each subagent reads only its batch and
writes one file, `drafts/<n>.md`**, of self-contained drafts:

```markdown
## <n>.<k> · <kind> · <target>
<the claim, in its final wording>
source: <path>, lines <a>-<b>
related: <n>.<j>, <m>.<i> | none
```

**A draft is written to be merged without its source**: the claim in the words the store would
receive, the source path and lines it came from, the target it would go to, and the drafts it touches.
**The merge reads the drafts, never the sources**, and that is what the threshold buys: a large run
pays to read each source once, in a subagent, and the run's own context holds only the drafts.

**`inventory.md` marks each source `drafted` as its batch's file is written**, so a pass stopped by a
usage limit or a closed session leaves a record of exactly what is done.

## Resuming

**An apply that stopped resumes by applying again** - `--apply` on that run. `log.md` shows each item
that started and did not finish; its write is checked against the target like any other item (the
report-apply rule's check before each write), so one that landed is recorded done and one that did
not is written.

**A pass that stopped resumes from its drafts.** An unfinished run is one with `drafts/` and no
`report.md`. **The bare command looks for one of its own in this store first**, by its tag, and asks
- *Resume <id> / Start a new run* - per the asking rule. **With no turn to answer in, it resumes.**
Resuming reads `inventory.md`, drafts what is not yet `drafted`, and merges. Starting new leaves the
old run as it is.

**A run that finished with work left is continued, not resumed** - an adopt run that stopped at
`adopt_max_bytes` with files `pending`. The next run is a new run item whose `requirements.md` names
the one it continues; the newest run's `resume.md` carries the pending set forward.

## Carried decisions

**A declined item stays declined.** Each finding kind names a **fingerprint** - the fields that make
two findings the same finding across runs - in its command's skill. The engine stores it on the item
and compares it.

**A fingerprint has two parts, `<key> · <change>`.** The **key** names the subject - a check and a
path, a source path, a set of entries - and the **change** is what brings a declined finding back: a
text's hash, a stamp, or nothing where the key alone decides. Each kind names both in its skill.

**A text's hash** is the text with every run of whitespace collapsed to one space and the ends
trimmed, hashed per the file-facts rule's *text hash* and cut to its first 12 characters, so a
reflowed line is not a change. **Write each item's text to `hash/<n>.txt` in the run item, then hash
them all in one call.** **No shell, or a call refused:** the items get no hash, and a kind whose change part is a hash carries nothing over - the
report says so.

**At phase 4, look each key up in this command's earlier runs** - the items tagged `run-<command>` in
`index.md`, newest first - by `Grep` on the fingerprint line in their `report.md`. The newest earlier
item with that key decides:

| There | Here |
|---|---|
| **declined** or **carried**, the same change part | **carried** - not proposed again; it names the run that declined it |
| **declined** or **carried**, a different change part | **open** - proposed again, saying which run declined it and what changed |
| anything else, or no earlier item | as found |

**A carried decision comes back only when its change part moves** - never because a later run was
asked differently or worded the finding another way.

**Every report says which decisions it carried and from which runs**, as items in the list - per the
report-shape rule, nothing reported outside it. A decision is undone by answering that item again in a
later run, never by editing an old one.

## Finding the run

| Asked for | Resolves to |
|---|---|
| `--apply <id>` | that run item - its id, or anything the item rule resolves to it |
| `--apply` | **the latest run of this command in this store** - the highest id tagged `run-<command>` |
| `/nk:load <run>` | the run, read from `report.md`'s states and `resume.md` - never a frontmatter status |
| `/nk:load`, `/nk:save` with no argument | **never a run item** - the inference passes over every item tagged `run-*` |

**`/nk:save` never writes into a run item.** A run's record is the run's own.

## Logging a write

**Every write an apply makes - in the store or outside it, a trim included - is logged in `log.md`
before it starts and after it lands**, one line each and in exactly this shape, since `/nk:doctor` and
a resumed apply grep for it:

```markdown
- 2026-10-19 · 3 · start · projects/app/gotchas.md - merge entries 4 and 7
- 2026-10-19 · 3 · done
```

A copy into `copies/` is logged as `- <date> · <n> · copy · <path> -> copies/<path>`, and an item
found stale at its check as `- <date> · <n> · stale · <what moved>`.

## Outside the store

**A kind that writes outside the store copies the file into `copies/` first**, byte for byte, per
the file-facts rule's *an exact copy*, at its path from its root: `home/` under `~`, `ws/` under the
workspace root, `repo-<name>/` under a registered repository, whichever is nearest. **`summary.md`
names the folder, and a revert is copying a file back.**
