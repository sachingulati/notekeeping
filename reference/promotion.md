---
doc: promotion
title: Promotion, contradictions and credentials
---

## Promotion

For each candidate fact: does it belong in the store at all, which file does its admission test send
it to (never a `bookkeeping` file), what is the narrowest scope covering every source that taught it, and is it already there.
**Apply the verdicts, then report every one of them** - what was written, where, and what was
skipped or held back and why. Promotion does not wait for a yes: the report is how you see it, and a
wrong entry is corrected like any other. `--dry-run` shows the verdicts and writes nothing.

**Under `--oneline` nobody sees that report**, so every entry this run promotes carries `unseen` as
the last part of its provenance stamp - `(0012 - 2026-09-28 - unseen)`. It is the one thing
`--oneline` changes in what is written, and it is there so an entry no person has read never looks
like one someone curated. **Reading it is what clears it**: whoever has read the entry deletes the
word by hand. `/nk:doctor` counts what is still unseen.

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
`areas/<topic>/gotchas.md`, an instruction to `areas/<topic>/instructions.md` - never to the
scope's register, where the area's keys would not send a session. A candidate touching no row goes
to the register; one touching two rows goes to the closer, and the report names the other.
***Already there* checks the area's file too.** **A promotion that gives a topic its third entry at a
scope with no area for it says so in one report line** - *3 entries on refunds - `/nk:review` would
make it an area* - and files the entry as usual.

### A contradiction

A candidate that contradicts an entry already in the store is **settled when that is cheap, and
never by picking the newer claim**. **For `save`**, take the first row that applies:

| The contradiction | Do |
|---|---|
| **The session already verified the new claim** - it read the file, ran the command, saw the value | **That is the check; do not run it again.** Write the new entry and mark the old one superseded, citing what the session saw |
| **A cheap check you can run now** - a file to read, a pattern to grep, a file under `.git` to read - never a command | **Run it.** Mark the losing entry superseded with what was checked and what it returned |
| **Checkable, but not now** - needs a running environment, a deploy, credentials | **Write the new entry and mark both unsettled**, naming the check verbatim |
| **Not mechanically decidable** - a policy, an intent, someone's plan | **Ask** which holds, as one of the run's open questions. Under `--oneline`, mark both unsettled with `Check: the user's call` |

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
