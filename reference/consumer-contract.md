---
doc:   consumer-contract
title: Being called by a tool, not a person
---

# The consumer contract

**A consumer is any tool that wants notekeeping's capabilities without owning its storage** - another
plugin, a hook, a scheduled agent, a wrapper someone wrote for themselves. It calls commands. It
never reads or writes the store directly.

`--caller <name>` declares **this call came from a tool**. The name is an opaque string used for
provenance and nothing else. **No command branches on its value** - a rule here may never acquire a
particular consumer's name.

**It does not declare that no human is present.** That is a property of the consumer, and consumers
vary: one has a user at the keyboard, one runs unattended for hours. Both get the same behaviour, and
the difference in what to do about it belongs to them.

## The four obligations

| Obligation | What it means here |
|---|---|
| **Never ask. Refuse instead** | The refusal names what is missing **and which argument supplies it**. A command that asked would reach around its caller to interrupt a flow it cannot see |
| **Never guess** | Refusal is the only alternative to asking. Disclosing a guess in prose does not count - nobody reads prose when a tool is driving |
| **End with the outcome line** | The call has no return value. Without a fixed last line the caller parses prose |
| **No-op when nothing changed** | A consumer checkpoints repeatedly. Report `no-change` and write nothing rather than churning a file |

## When more than one thing is missing

**Validate the call before touching the filesystem.** A refusal names the argument that would
satisfy it, so **which check fires first decides what the caller retries with** - and a consumer
told *the store is missing* when the real problem is a missing flag value retries the wrong thing,
or stops.

**The order is the flags, then the store, then the project.** Cheapest first, and it also produces
the most actionable refusal: an argument the caller controls is one it can fix without a person,
while a missing store is not.

*(Measured, session 26, on a flag since removed: the same `--caller` call refused twice for different reasons on
two runs - once naming the missing flag value, which was right, and once naming a store that was
present. Two refusals for one call is one refusal too many.)*

## The outcome line

**The last line matching `^nk: `** - and the only part of the output a consumer may depend on.

**Match it; do not take the last line blindly.** Under `--caller` it is the only line there is, so
the two are the same thing in the normal case - but a consumer that *matches* is unharmed by
anything that ever went wrong here (a wrapping fence, a preamble, a trailing remark), and a consumer
that takes the last line is broken by all three. **The producing rule is strict and the parsing rule
is tolerant**, deliberately: one of them is enforced by a prompt and the other by code, and only the
second can be relied on absolutely.

```
nk: <command> <status> — <detail>
```

**Under `--caller` the outcome line is the whole output. There is no report.** This is the rule
above applied to reporting and not only to asking - **a consumer is not a reader**, and any prose at
all is prose the line can get lost behind.

**Every contract command emits exactly one line and nothing else.** No heading, no summary, no
findings, no code fence, nothing before it and nothing after it. **A command whose job is to report
compresses its findings into `<detail>`** - `2 errors, 2 warnings, 0 info` is a report, and it fits.

**This is structural, not stylistic.** With nothing else in the output there is nothing for the line
to be buried behind, and its absence is an empty response rather than something a caller has to
detect. **A command file that describes an output shape does not apply under `--caller`** - if one
appears to, this rule wins and the command file is wrong.

**Do not narrate this rule.** Saying *"since `--caller` is set, only the outcome line is emitted"*
**is** the report it is suppressing, and it is the one failure mode left once the report is gone.
Emit the line. Say nothing about emitting it.

*(Measured, session 26, across three rounds and 32 contract calls. **Round 1:** one call in nine
ended `` ``` ``; diagnosed as a wording problem, and the wording was fixed. **Round 2, after that
fix: two in nine** - one fenced, one omitting the line entirely and ending on a free-form
"Discrepancies" section. **Round 3, with the report suppressed except for a bounded block in `load`
and `doctor`: two in eleven, both of them in the block-keeping commands, and zero in the commands
emitting the line alone.** The block was the defect at every round; the carve-out kept it alive.
**Root cause:** `load.md` carried its own `## Output - one screen` section ending in *"the
discrepancies section is the point"* - a shipped command file contradicting this one, and winning.)*

| Status | Means |
|---|---|
| `ok` | it wrote what it was asked to write. Usually the store; **also a projection**, where one was enabled |
| `no-change` | the call was valid and nothing needed writing |
| `refused` | the command did not run, and the detail says what would let it |

```
nk: save ok — work/2026-09/spike-auth, 3 files
nk: save no-change — work/2026-09/spike-auth
nk: work refused — project unresolvable; pass --project, or run /nk:index to see the options
```

**`no-change` is what makes idempotency observable**, and it is the status that separates a consumer
that can safely retry from one that must track state itself.

**Emit the line only under `--caller`.** Without the flag a person is reading, and the ordinary
report is the output.

## Every command accepts `--caller`. Not every command will act on it

**All sixteen accept the flag**, because a consumer can invoke any of them - nothing stops an agent
typing `/nk:init` - and a command with no defined behaviour under `--caller` does not become
unreachable, it becomes **unpredictable**. Accepting the flag is what makes the answer a parseable
refusal instead of whatever the command happens to do with an argument it does not recognise.

**Accepting the flag is not authority to act.** These two questions were fused into one table until
session 37, and separating them is the whole of this section:

| | Under `--caller` |
|---|---|
| `work` `save` `load` `index` `doctor` `plan` `summary` `test` `how` `api` `project` | **act normally**, within the four obligations |
| `budget` `help` | **act normally** - they only ever read. `help` is the discovery surface: it answers with the contract version and the callable list, so a consumer built against an older contract can tell |
| **`review`** | **reports, never applies.** Compress the findings into `<detail>`. `--apply` and `--apply all` are refused: the write depends on a per-finding selection, and a consumer cannot supply one |
| **`init`** | **reports, never creates.** Say what it would create and that a person must run it |
| **`config`** | **reports, never writes.** Resolved values are readable; `set` is refused |

**A consumer never initialises a store and never edits configuration.** That rule is unchanged and is
now enforced *inside* the two commands rather than by their absence from a list. No store means a
refusal naming `/nk:init`, run by a person - which is what keeps the machine config reachable only by
the person whose machine it is.

**Why the refusal beats the omission.** A command outside the contract answered an unrecognised flag
with undefined behaviour, and the caller learned nothing it could act on. A command inside it answers:

```
nk: init refused - a store is created by a person; run /nk:init
nk: config refused - settings are edited by a person; /nk:config set is not available to a caller
nk: review refused - --apply needs a per-finding selection; report the findings to your user
```

**The old table also contradicted itself**, which is how this was found: `help` sat in the *Not* row
while this same file specified its `--caller` behaviour two paragraphs below, calling it *"the one
exception to the table"*. An exception that permanent is a table with the wrong shape.

## The refusals a consumer will actually hit

These are the ask-points that become refusals. Each names its argument, because a refusal that names
none is unactionable.

| Situation | Refusal must name |
|---|---|
| No store above the working directory | `/nk:init`, run by a person - never create one |
| The project cannot be resolved | `--project <name>`, and `/nk:index` to list the options |
| `save` has something to promote | `--promote auto` or `--promote none`. **Supplying neither refuses naming both** - guessing which was meant is the silent promotion this forbids |
| `init` or `config set` called by a consumer | **a person.** The action is refused whatever the arguments are, and the detail says so rather than naming a flag that would unlock it - there is none |
| `review --apply` called by a consumer | **a per-finding selection**, which a consumer cannot supply. Report the findings to the user instead |

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

## What this does not solve

**No locking.** Two consumers, or a consumer and a user, working one store concurrently is unhandled
in v1 - a stated limit rather than an unnoticed one. Contradiction handling is what catches the
result, after the fact.
