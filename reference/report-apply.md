---
doc:   report-apply
title: A run's report, the question it asks, and --apply
---

# Applying a report

**The report itself - one numbered list, the count read off it - is the report-shape rule. The run
item that holds it - `report.md`, `log.md`, its states - is the run-engine rule.** This is what
happens after the report is printed.

## A yes applies what was shown

**A proposal and the write that follows it are one decision, and the run's `report.md` carries it.**
What was shown is the report's items - every one, with the answer each was given - and that is what
gets written. Nobody re-runs the pass to act on what they just read.

**The question.** After `report.md` is written, ask once, with `AskUserQuestion`:
**Apply / Publish as a page / Change answers / Not now.** *Publish as a page* appears only where the
skill offers its report as a page, and before anything is applied: it publishes the report and asks
this question again, with *Apply the page's selection* in its place. An item with a question of its
own - *merge into which entry?* - shows it in the report, numbered with its item, before the
question. **`/nk:adopt` asks a question of its own** - *Apply all and trim / Apply all / Review as a
page / Show here*, and after a review *Apply / Apply and trim / Trim only / Not now* - per its skill;
everything below holds for its questions as for this one, except that a typed answer to its
**first** question is carried out as the pick it names, per its skill, rather than asked again.

| Pick | Do |
|---|---|
| **Apply** | read `report.md` - its items, their states and answers - **never the conversation**; apply every open item with something to write, checking each before its write (below) |
| **Publish as a page** | publish the report, per the report-pages rule; then ask again, with the next pick in its place |
| **Apply the page's selection** | read the selection the page kept (the report-pages rule, *The selection comes back*); write it into `report.md` as answers - the items picked, everything else *skip* - show it, then *Apply*. A typed answer naming the page - *apply what I ticked* - is this pick |
| **Change answers** | ask again, the current answers shown |
| **Not now** | the run stays as it is, and a later `--apply` applies it |

**Three answers an item can take, beside what it proposed:**

| Answer | The item's state | Next run |
|---|---|---|
| **skip** - *not 4*, *skip 4* | skipped | finds it again |
| **decline** - *decline 4*, *never 4*, *decline all* | declined | **carries it** - not proposed again until its change part moves (the run-engine rule) |
| **its own question's answer** - *3: merge into auth* | stays open, the answer recorded | applies it as answered |

**Declining is the only way a finding stops coming back**, and it is said in words: a skip is *not
now*, never *no*.

**An answer typed rather than picked is not applied in the turn it arrives in** - *yes*, *yes but
not 4*, *decline 7*, *3: merge into auth* alike. The skill is started again with its original
arguments, and that run is a **re-entry** when the conversation holds this skill's question and an
answer after it, **and the run that question named still has an open item**. Otherwise the run is
fresh, and passes again.

**On a re-entry there is no pass.** Read the run's `report.md`; write each answer onto its item - the
`answer` line, the later answer replacing the earlier, and the state where the answer sets one - a
plain *yes* answering every open item with something to write; show each item's resolved write; then
ask the question again.

1. **What was approved is what gets written** - every open item the answers leave in, and nothing
   the report does not hold. **Nothing is classified, routed or numbered again**: a second derivation
   can land somewhere defensible and different, and then what was approved and what was written are
   two things nobody reconciled.
2. **An answer can narrow the list** - *"yes, but not 4"*, *"only the store"* - and the narrowed
   list is then what gets written. **It never adds to it.**
3. **Before each write, check that what the item read has not moved** - the text it quoted, the
   count it printed. Where it has, **write nothing for that item**: mark it **stale**, log it, and go
   on to the next. This checks that the approval still describes the material; it is not a second
   decision. Applying a run twice is therefore safe: what was already written no longer matches.
4. **No turn to answer in means nothing is written.** The run item is kept, and a later `--apply` is
   how it gets applied. **No flag approves a proposal before it exists.**
5. **`--dry-run` shows the proposal, does not ask, and mints nothing.**

## Each write is logged

**Every write an apply makes is logged in `log.md` before it starts and after it lands**, in the
shape the run-engine rule's *Logging a write* gives. The item's state moves to **applied** only after
its `done`. A write outside the store
copies its file into `copies/` first, logged too. **That is what makes a stopped apply resumable**:
the next `--apply` on the run finds every item with a `start` and no `done`, and checks its target
before writing.

## `--apply`

**`review`, `adopt` and `upgrade` take `--apply`**, and it acts on a run that already exists - never
on a proposal that has not been made yet. **`/nk:doctor` has none** - `--fix` acts on what the same
run just found, immediately - but its runs are items like any other, and their answers carry.

**`--apply [<run>] [all | <numbers> | <categories> | <text>]`:**

| Part | Means |
|---|---|
| `<run>` | a run item - its id, or anything the item rule resolves to it. **Omitted: the latest run of this command in this store** (the run-engine rule, *Finding the run*) |
| `all` | every open item with something to write |
| `<numbers>` | those items - `1,3,7`, or a range, `1-319`. A number is read against the report, so it cannot drift |
| `<categories>` | where the report has lettered categories (`/nk:adopt`), every item in them - `A,C`; letters and numbers mix, `A,C,412` |
| `<text>` | narrows within the report where the command accepts it - *"only the gotchas"*. It never reaches past the report |
| nothing | walk the run's open items one at a time: show each write, then ask with one `AskUserQuestion` per item - *apply*, *skip*, *decline* or *stop*. **With no turn to answer in, a walk cannot start** - apply nothing, and say `all` or numbers are needed |

**Say which run you are applying before anything else** - the first line, before any check or
write: its id and title, the command line it ran, its date, and its items by state. *"Applying 0034
- Review run on web-client, 2026-09-28, `/nk:review web-client`: 14 items - 9 open, 3 declined, 2
carried."* Whoever typed `--apply` - especially with no run named - sees which run they are approving
before it is carried out.

**No run of this command, or the one named is not a run of it?** Say so, name the bare command that
makes one, and write nothing. **A run with no open item to write** says so and applies nothing.

**After applying, the record is the run's own**: the states in `report.md`, the lines in `log.md`,
and `summary.md` and `resume.md` rewritten, per the run-engine rule's phase 7. The terminal says what
was written, skipped, declined and stale - counted from `log.md` and the states, never from the
proposal.
