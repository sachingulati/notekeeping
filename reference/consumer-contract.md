---
doc:   consumer-contract
title: Being called by a tool, not a person
---

# The consumer contract

| | |
|---|---|
| **The contract version this plugin ships** | **3** |

**It is stated here and nowhere else**; `/nk:help --oneline` reports it by reading this line. **It
moves only
when an existing consumer would have to change**: a new command or a new flag leaves it where it is.

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
| **asks a question** where it cannot go on | **stops with `asks`**, and the line carries the question: what is missing, the argument that supplies it, and **the options, where the run has them** |
| **offers a page** (the report-pages rule) or makes any other closing offer | **offers nothing.** An offer is a question with nowhere to go, and it never blocked the run |

**`asks` is the question, handed back.** A consumer with a user at the keyboard can put it to them
and call again with the answer; one without can stop. Either way the command never interrupts a flow
it cannot see:

```
nk: save asks — 2 items match: 0012-auth-timeout, 0015-auth-ui; pass [id]
nk: work asks — project unresolvable; pass --project <name>, or run /nk:project to see the options
```

**`refused` is a stop no answer fixes**: what was asked cannot be done as asked - an unknown key, a
path the command never writes, a store that is not empty. The detail says why, and calling again
unchanged gets the same line. A person's run stops there too, without a question:

```
nk: config refused — unknown key notes_depth
```

**`--oneline` does not declare that no human is present.** Consumers vary: one has a user at the
keyboard, one runs unattended for hours. Both get the same behaviour, and the difference in what to do
about it belongs to them.

**One consequence for what gets written.** A save promotes without asking, because the report is how
a person sees what went where - and under `--oneline` nobody sees that report. So **every entry
promoted in a `--oneline` run carries `unseen` in its provenance stamp**, per
the promotion rules, and `/nk:doctor` counts what is still unseen.

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
| `ok` | it did what it was asked. Usually that is a write - the store, and **also a read line** where registration wrote one. For a command that only reads, and **under `--dry-run`**, it is the report: the detail leads with `dry run` where that flag was given, and says what would have been written |
| `no-change` | the call was valid and nothing needed writing |
| `asks` | the command stopped where a person's run would have asked, and the detail carries the question and the argument that answers it. Call again with it |
| `refused` | the command cannot do what was asked, and the detail says why. Calling again unchanged gets the same line |
| `failed` | the run broke down partway through, after some of it had already written. The detail names what was and was not written |

```
nk: save ok — work/2026-09/0012-spike-auth, 3 files
nk: save no-change — work/2026-09/0012-spike-auth
nk: save failed — wrote requirements.md; drain not attempted
nk: plan ok — dry run; would write work/2026-09/0012-spike-auth/plan.md
nk: plan ok — work/2026-09/0012-spike-auth (inferred from branch)
```

**Where the run acted on an inference, the detail says so** - `(inferred from branch)`. A person
reads that in the report; a consumer can only read it here.

**No line at all is not `no-change`.** A caller that never sees an outcome line - the connection
dropped, a usage limit was hit, the run stopped before printing - reads that as *interrupted, state
unknown*, never as nothing having happened.

**`no-change` is what makes idempotency observable.** Writing nothing when nothing changed is every
command's rule, not this contract's (the store write rules, *Every
run*); `no-change` is how the line reports it.

**Emit the line only under `--oneline`.** Without the flag a person is reading, and the ordinary
report is the output.

## A report that does not fit

**Most commands fit** - `save`, `load`, `work`, `plan`, `test`, `summary`, `index`, `init`,
`config`, `budget` and `help` answer in the detail. **`review`, `doctor`, `adopt`, `upgrade` and a
bare `project` do not**: their report is a list someone has to read.

**`review`, `adopt` and `upgrade` save every report - it is what `--apply` applies**, per
the report shape's *The report is saved*. **`doctor` also saves one,
every run, but it has no `--apply`.** A bare `project` saves one only under `--oneline`. Either way
**the line names the file by absolute path:**

```
nk: review ok — 14 findings, 9 applyable; nothing written; details: <store>/tmp/review-20260928-2.md
```

- **One file per run**, named `<command>-<YYYYMMDD>-<n>.md` - a same-day counter, never a time - and
  never overwritten, so two consumers calling at once never overwrite a report the other is still
  reading.
- **The file is the report a person would have seen**, in the same shape - never more than that.
- **The directory is created by the first report written into it**, together with a
  `tmp/.gitignore` holding `*`, so a store kept under git never shows a report as a change.
- **It is output, not store content.** Only `--apply` reads it back. `/nk:doctor --fix` empties
  reports that were applied or are more than a week old.

**A command that only reads never writes one** - it compresses into the detail, or names a store
file that already holds the answer.

## Why a consumer calls rather than reads

1. **Every shared rule is cited by a path built on `${CLAUDE_PLUGIN_ROOT}`**, and that variable
   resolves to the *calling* plugin's root - a consumer citing it lands in its own directory and
   finds nothing.
2. **The file layout stays private** rather than freezing into public API.
3. **The invariants in the store rules and the definition-resolution rule are enforced once** instead of
   being reimplemented per consumer.
4. **`allowed-tools` is declared per command** and the harness enforces it, so a consumer routed
   through the commands inherits those restrictions.

**One store, one writer at a time.** Nothing here locks, so two consumers - or a consumer and a
user - checkpointing the same store concurrently can interleave.
