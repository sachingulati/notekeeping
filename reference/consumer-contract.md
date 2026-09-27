---
doc:   consumer-contract
title: Being called by a tool, not a person
---

# The consumer contract

| | |
|---|---|
| **The contract version this plugin ships** | **1** |

**It is stated here and nowhere else.** `/nk:help --oneline` reports it by reading this line - a
number typed into a command file is one that drifts the first time the contract moves, and a
consumer built against a drifted number cannot tell which behaviour it is getting. **It moves only
when an existing consumer would have to change**: a new command or a new flag leaves it where it is,
exactly as a plugin release leaves `schema_version` alone
(`${CLAUDE_PLUGIN_ROOT}/reference/schema-version.md`, which keeps the same distinction for stores).

**A consumer is any tool that wants notekeeping's capabilities without owning its storage** - another
plugin, a hook, a scheduled agent, a wrapper someone wrote for themselves. It calls commands. It
never writes the store directly, and reads only the report file a line names (below).

## `--oneline` changes the output, and nothing else

**Every command does exactly what it does for a person.** It resolves the same way, infers the same
way, acts on the same confident inference and stops where a person's run would stop. **A flag is
approval, whoever types it**: `--apply` on a saved report and `--fix` work under `--oneline` exactly
as they do when a person types them, and a consumer answers to its own user for the flags it passes. **Only the output differs**, in three ways:

| A person's run | Under `--oneline` |
|---|---|
| **prints its report** | **prints one line** - the outcome line, below. A report that does not fit goes to a file the line names |
| **asks a question** where it cannot go on | **refuses**, and the line carries the question: what is missing, the argument that supplies it, and **the options, where the run has them** |
| **offers a page** (`${CLAUDE_PLUGIN_ROOT}/reference/report-pages.md`) or makes any other closing offer | **offers nothing.** An offer is a question with nowhere to go, and it never blocked the run |

**A refusal is the question, handed back.** A consumer with a user at the keyboard can put it to them
and call again with the answer; one without can stop. Either way the command never interrupts a flow
it cannot see:

```
nk: save refused — 2 items match: 0012-auth-timeout, 0015-auth-ui; pass [id]
nk: work refused — project unresolvable; pass --project <name>, or run /nk:project to see the options
```

**`--oneline` does not declare that no human is present.** Consumers vary: one has a user at the
keyboard, one runs unattended for hours. Both get the same behaviour, and the difference in what to do
about it belongs to them.

**One consequence for what gets written.** A save promotes without asking, because the report is how
a person sees what went where - and under `--oneline` nobody sees that report. So **every entry
promoted in a `--oneline` run carries `unseen` in its provenance stamp**, per
`${CLAUDE_PLUGIN_ROOT}/commands/save.md` *Promotion*, and `/nk:doctor` counts what is still unseen.

## The outcome line

**The last line matching `^nk: `** - and the only part of the output a consumer may depend on.

**Match it; do not take the last line blindly.** Under `--oneline` it is the only line there is, so
the two are the same thing in the normal case - but a consumer that *matches* is unharmed by
anything that ever went wrong here (a wrapping fence, a preamble, a trailing remark), and a consumer
that takes the last line is broken by all three. **The producing rule is strict and the parsing rule
is tolerant**, deliberately: one of them is enforced by a prompt and the other by code, and only the
second can be relied on absolutely.

```
nk: <command> <status> — <detail>
```

**Under `--oneline` the outcome line is the whole output.** No heading, no summary, no findings, no
code fence, nothing before it and nothing after it. **A command file that describes an output shape
does not apply under `--oneline`** - if one appears to, this rule wins and the command file is wrong.

**Do not narrate this rule.** Saying *"since `--oneline` is set, only the outcome line is emitted"*
**is** the report it suppresses. Emit the line. Say nothing about emitting it.

| Status | Means |
|---|---|
| `ok` | it did what it was asked. Usually that is a write - the store, and **also a projection** where one was due. For a command that only reads, and **under `--dry-run`**, it is the report: the detail leads with `dry run` where that flag was given, and says what would have been written |
| `no-change` | the call was valid and nothing needed writing |
| `refused` | the command stopped where a person's run would have asked, and the detail carries the question |

```
nk: save ok — work/2026-09/0012-spike-auth, 3 files
nk: save no-change — work/2026-09/0012-spike-auth
nk: plan ok — dry run; would write work/2026-09/0012-spike-auth/plan.md
nk: plan ok — work/2026-09/0012-spike-auth (inferred from branch)
```

**Where the run acted on an inference, the detail says so** - `(inferred from branch)`. A person
reads that in the report; a consumer can only read it here.

**`no-change` is what makes idempotency observable.** Writing nothing when nothing changed is every
command's rule, not this contract's (`${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, *Every
run*); `no-change` is how the line reports it.

**Emit the line only under `--oneline`.** Without the flag a person is reading, and the ordinary
report is the output.

## A report that does not fit

**Most commands fit** - `save`, `load`, `work`, `plan`, `test`, `summary`, `index`, `run`, `init`,
`config`, `budget` and `help` answer in the detail. **`review`, `doctor`, `adopt`, `upgrade` and a
bare `project` do not**: their report is a list someone has to read.

**The first four save every report anyway** - it is what `--apply` applies, per
`${CLAUDE_PLUGIN_ROOT}/reference/report-shape.md`'s *The report is saved*. A bare `project` saves
one only under `--oneline`. Either way **the line names the file by absolute path:**

```
nk: review ok — 14 findings, 9 applyable; nothing written; details: <store>/.notekeeping/tmp/review-20260928-143012.md
```

- **One file per run**, named `<command>-<YYYYMMDD-HHMMSS>.md`, so two consumers calling at once never
  overwrite a report the other is still reading.
- **The file is the report a person would have seen**, in the same shape - never more than that.
- **The directory is created by the first report written into it**, together with a
  `tmp/.gitignore` holding `*`, so a store kept under git never shows a report as a change.
- **It is output, not store content.** Only `--apply` reads it back. `/nk:doctor --fix` deletes
  reports that were applied or are more than a week old.

**A command that only reads never writes one** - it compresses into the detail, or names a store
file that already holds the answer.

## Why a consumer calls rather than reads

The third reason is decisive and is a fact about the harness, not a preference: **every shared rule
is cited as `${CLAUDE_PLUGIN_ROOT}/reference/...`, and that variable resolves to the *calling*
plugin's root.** A consumer citing it lands in its own directory and finds nothing. Direct access is
not merely discouraged - it is unimplementable without a second copy of the boundary rule, which
drifts from the first.

The other two: the file layout stays private rather than freezing into public API, and the invariants
in `store-boundary.md` and `schema/resolution.md` are enforced once instead of being reimplemented
per consumer. A fourth follows for free - `allowed-tools` is declared per command and the harness
enforces it, so a consumer routed through the commands inherits those restrictions.

**One store, one writer at a time.** Nothing here locks, so two consumers - or a consumer and a
user - checkpointing the same store concurrently can interleave. Contradiction handling catches the
result after the fact.
