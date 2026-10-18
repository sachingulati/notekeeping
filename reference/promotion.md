---
doc: promotion
title: Promotion, contradictions and credentials
---

## Promotion

For each candidate fact: does it belong in the store at all - *Worth recording*, below - which file does its admission test send
it to (never a `bookkeeping` file), what is the narrowest scope covering every source that taught it, and is it already there.

**The scope is the narrowest store enclosing every source** - the project, the workspace, a
workspace enclosing it, or global. **A wider rung is earned twice**: the sources must span it, and
the entry must pass *Worth recording* judged for every session that rung loads - would a session
anywhere beneath it do worse without it? **An entry that fails is not stored** - there is no fallback
to a narrower rung, which would cover only some of its sources. Being about no codebase is never a
reason to widen: global is paid for in every session on the machine. **For a workspace that encloses
others, *here* and *this workspace* in its definitions mean everything beneath it** - its own
projects and every project of every workspace inside it; its admission tests are otherwise the same.
**Apply the verdicts, then report every one of them** - what was written, where, and what was
skipped or held back and why. Promotion does not wait for a yes: the report is how you see it, and a
wrong entry is corrected like any other. `--dry-run` shows the verdicts and writes nothing.

**A scope the user named is the scope.** Where the session shows the user asking for a fact to go to
a particular level - *put this in the workspace notes*, *this is global* - route it there and propose
no other, even where the narrowest-scope rule would pick a nearer one. That is the user's decision,
not a candidate for correction. **A topic the user named narrows the candidates** - *save what we
learned about deployments to the workspace* promotes everything the session taught about that topic,
across whichever files it belongs in. The verdicts still apply inside that scope: already there is a
duplicate, a conflict is still reported.

**Applying is not "promote everything".** A duplicate is skipped, and a contradiction is settled as
below - never silently.

**An area takes its topic's entries.** Where the scope holds `areas/INDEX.md`, read it before
routing: a candidate that touches a row's keys goes to that area's file of the same type -
`areas/<topic>/gotchas.md`, an instruction to `areas/<topic>/instructions.md`, **creating that file
where the area has none** - never to the scope's register, where the area's keys would not send a
session. A candidate touching no row goes
to the register; one touching two rows goes to the closer, and the report names the other.
***Already there* checks the area's file too.**

### Organising as it writes

**Entries are kept organised when they are written** - by `/nk:save` and `/nk:adopt` as they write,
and by `/nk:review` over what is already there (its findings 1 to 3) - because a register left to
grow loose is read loose by every session until someone tidies it.

**Related entries are merged.** Where a candidate and an entry already there - or two candidates -
each say part of one thing, write one entry that combines them: what each says and how it says it,
**dropping nothing either records**, both corrections kept. The merged entry keeps the file's entry
format; one that would outgrow it is a topic, below. The report names each merge and the entries it
joined. Entries that only share a subject are not merged.

**A topic that has earned a shelf gets an area.** Count a topic's entries in **every register at
that scope** - `gotchas.md`, `patterns.md`, `runbook.md`, `domain.md` and the rest, not only the file
the candidate goes to - with the candidates included. An entry counts when it is about the topic,
whatever its kind and whatever else it names: a gotcha on retrying refunds and a pattern on refund
idempotency keys are two refund entries. **Report the count with where each entry sits.** Where
there is no area for it yet:

| Writer | Creates the area when |
|---|---|
| `/nk:adopt`, `/nk:review` | the topic has **three or more entries** - proposed in the report, created when the report is applied |
| `/nk:save` | the topic reaches three entries **and at least two of them were already there before this save** - in the registers the count reads anyway, so no work item is read. One save's entries never make an area on their own |

**Creating one**: move the topic's entries, unchanged, into `areas/<topic>/` - each into the file of
its own type, which keeps that register's shape and threshold - leave one pointer line in each
register they left, *See `areas/<topic>/gotchas.md` - <what it covers> (<n> entries)*, and write the
topic's row in `areas/INDEX.md`. **Where `areas/INDEX.md` did not exist, the same write adds
`NOTES.md`'s two area lines**, per the `NOTES.md` definition: an index nothing reads is never read.
**The report names every area created** and the entries it took. A save does not ask first: nothing
is lost, and the report shows it.

**A topic short of that test** is filed as usual, and the report says it is building - *2 entries on
refunds, 1 from this save*.

### Worth recording

**An entry earns its place only if a later session would do worse without it** - take a wrong turn,
redo work, ask the user again, or miss something it had no way to know to look for. Admission says
which file a fact belongs in; this says whether it belongs anywhere. Each of these fails it:

| Not worth recording | Because | Instead |
|---|---|---|
| **Derivable when it is needed** - a version a command prints, a value a config file holds, what a script or alias does, what the code or git shows | the source is current and a copy goes stale | nothing - or, where a session would not know to look, one line saying **when** to reach for it and what for: *interrupted transfers: `sync-retry` (`~/bin`) resumes where the last run stopped* |
| **A list with nothing to act on** - names of aliases, files or tools with no when and no why | a line nobody acts on is paid for on every read | drop it, or give each item its *when* |
| **One-off** - the conclusion of one session, one investigation or one note, binding nothing beyond it | it is history, not what the next task needs | the note or work item it came from, where it already is |
| **What any capable session does anyway** - generic good practice | it restates the default | drop |
| **Said elsewhere** - the same fact in another file or scope | two copies drift | the one place it fits, merged |
| **Loaded always, needed only when a task reaches it** | paid on every prompt for one task in ten | its on-demand register, and a trigger line where the agent would not otherwise look |
| **About the record itself** - what a file was, when it was written, that it loads | it describes the container, not the work | drop |

**Derivable means cheap and obvious to derive.** A trap the config hides, why a setting is what it
is, a constraint between two versions (*the 3.x client needs server 2.4 or later*) - none is derivable in
this sense, and each stays. **Where it is unclear, move it on-demand rather than drop it**: a drop is
for what clearly fails.

### A contradiction

A candidate that contradicts an entry already in the store is **settled when that is cheap, and
never by picking the newer claim**. **For `save`**, take the first row that applies:

| The contradiction | Do |
|---|---|
| **The session already verified the new claim** - it read the file, ran the command, saw the value | **That is the check; do not run it again.** Write the new entry and mark the old one superseded, citing what the session saw |
| **A cheap check you can run now** - a file to read, a pattern to grep, a read-only git command - never one that changes anything | **Run it.** Mark the losing entry superseded with what was checked and what it returned |
| **Checkable, but not now** - needs a running environment, a deploy, credentials | **Write the new entry and mark both unsettled**, naming the check verbatim |
| **Not mechanically decidable** - a policy, an intent, someone's plan | **The user stated it in this session as decided?** That is their call, made: write the new entry and mark the old one superseded, citing them. **Otherwise ask** which holds, as one of the run's open questions. Where nobody can answer, mark both unsettled with `Check: the user's call` |

**For `adopt`**, which is setting the store up and may be in no state to check anything: **write
both claims.** Where the material being adopted, or the store's own notes, settles the pair, mark the
losing entry superseded with that evidence. **Otherwise make one light read per pair** - one `Read`
of a file a claim names, or one `Grep` for its key term - and nothing is run. Still unsettled, mark
both unsettled; **no question is asked mid-pass**. **The trim follows the recording, an unsettled
pair included**: the store holds both texts and their sources, and a line left in a context file
keeps loading a possibly wrong claim into every session.

**Nothing is deleted** - verification decides which claim is current, never which is kept. **The
evidence is recorded, not just the verdict.** **A check that cannot run is not a resolution**: it
drops to the next row, never to the newer entry. The notes go on their own line under the entry:

```markdown
  > **Superseded 2026-09-28** by the entry below. Checked: `Grep "timeout" config/api.yml` → `60s`.
  > **Unsettled 2026-09-28.** Contradicts "<the other entry's first words>". Check: <the check, verbatim>.
```

`/nk:doctor` lists every unsettled note; `/nk:review` proposes a check that has since become cheap.

## Credentials are never copied

**A credential, token, password or key is never copied into a store, from any source** - a file, a
section, a memory item, or the session itself. **A faithful copy copies secrets.** Anything lifted
into a store can reach a file every session there reads, and so every prompt in that repository -
**and nothing un-reads a secret that has been in a context window**, so this is a gate rather than a
caution.

1. **Never write content from a source that looks like it holds a credential** - a key, a token, a
   password, a connection string carrying one, a private key block.
2. **Be wrong in one direction only.** Excluding something harmless costs a line in the report;
   including a credential cannot be undone.
3. **An exclusion is not a row anyone can say yes to.** No yes reaches it. Report it,
   with its path and the reason, under its own heading.
4. **An excluded line is never removed from where it is.** It did not reach a store, so nothing that
   finishes a move has licence to touch it - and a secret deleted from the only file holding it is the
   worst outcome available.

**The one exception is the user asking, in this session, for a credential to be kept** - *save the QA
login*, *remember this token*. Then it goes to `secrets.md` at the narrowest scope it applies to - a
project's test login to that project, a machine-wide token to global - and to nowhere else, per that
file's definition. **Asked means asked**: a credential the session saw or used, a request from an
earlier session, a memory item and a yes to a proposal are not requests, and stay excluded.
