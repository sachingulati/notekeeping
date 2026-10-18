---
file:        report.md
scope:       work
schema:      4
enabled:     true
shape:       bookkeeping
owner:       nk:adopt, nk:doctor, nk:review, nk:upgrade - the run engine
trigger:     a run item's pass ends with at least one item
authority:   original
budget:      none - grepped by fingerprint, read whole only by its own run
---

## Question
What did this run find, and where does each item stand?

## Admission
Every item the run reported, numbered as it was printed, each with its state, its fingerprint, the
write it would make, the text it read to propose it, and the answer it was given.

## Exclusion
What the run looked at and found nothing in -> `inventory.md`. What was written, when -> `log.md`.
What the run did, in plain words -> `summary.md`. What to do next -> `resume.md`.

## Entry format
```markdown
<!-- nk: schema 4 -->
# <the run's title>
Run <id> · /nk:<command> <arguments> · <store> · <date>

14 items - 9 to apply (A-C), 2 with nothing to write, 3 carried

## A · <category>
### 1 · <kind> · <subject>
- **state** open
- **fingerprint** `<key> · <change>`
- **write** <file> - <what changes>
- **read** <path>, lines <a>-<b>
  > <the text it read>
<the item as the run printed it>
```

**The header names the run, and the arithmetic line follows the report-shape rule** - the count is
the last item's number, and every split is printed as a sum that reconciles to it. A publish adds one
line, `Page: <url>`, below the header. **A category heading appears only where the command letters
its categories**; otherwise items follow the arithmetic line directly.

**`write` is on an item with something to write, and `read` on an item that quoted something.** An
item with nothing to write - a flag for a human, a check to run - carries neither `write` nor an
answer that applies, and is still numbered.

**Two lines join an item as it moves**: `- **answer** <the answer>` once one is given, the later
answer replacing the earlier, and `- **from** <run id>` on an item carried or brought back from an
earlier run, with what changed where it came back.

## States
| State | Means | Carried into the next run |
|---|---|---|
| **open** | proposed and not decided | no |
| **skipped** | answered *skip* - left for now, not refused | no - the next run finds it again |
| **declined** | answered *decline* - the user does not want it | **yes** |
| **carried** | declined in an earlier run, and its change part has not moved | **yes** |
| **applied** | its write landed - `log.md` holds it | no |
| **stale** | what it read moved after the report; nothing was written | no - the next run proposes it afresh |

**The `state` and `answer` lines are the only lines rewritten after the report is written**, and
`Page:` the only one added. Everything else stays as printed, so a number means the same thing for the
life of the run.

## Migration
**3 -> 4 - gather.** A release-3 store keeps its reports in `.notekeeping/tmp/`. Each becomes a run
item, and each is outstanding while its source is still there. Run this step **before**
`inventory.md`'s, which places `adopted.md`'s rows into the run items it mints.

- **`tmp/<command>-<YYYYMMDD>-<n>.md`** becomes a run item of that command, minted per the bundle
  shape: the title *<Command> run, <date> (from tmp/<name>)*, `created` the report's date, the bucket
  that date's, `project:` the project its header names as the run's scope or empty. Its header -
  every line before its first `## ` - becomes `requirements.md`'s body, verbatim.
- **Its `report.md` is the old report's text, verbatim**, below the header the entry format gives -
  the arithmetic line counted from the states. Each item gains a `### <n> · <kind> · <subject>`
  heading where it has none of its own, and its `state` and `fingerprint` lines; nothing else is
  added, and no line of the old report is changed or dropped. An item its `## Applied` lists as
  written is **applied**, as stale **stale**, an answer of *skip* **skipped**, and every other item
  **open**; its `## Answers` become each item's `answer`.
- **Every converted item's `fingerprint` is `none - converted`**, and carries nothing over: a
  release-3 report holds no declined item, and only a declined or carried item is ever looked up.
- **Its `## Applied` becomes `log.md`'s lines** - per applied item a `start` naming its kind and
  subject, then `done` - dated as the section is.
- **It gets `resume.md`**, its states counted, and no `summary.md`: the report is the whole record
  of that run.
- **`tmp/<command>-<YYYYMMDD>-<n>.html`** becomes that run item's `report.html`, byte for byte.
- **`tmp/adopt-<YYYYMMDD>-<n>/`**, the trim copies, becomes that run item's `copies/`, byte for byte,
  at the same paths.
- **`baseline.md`** becomes one doctor run item, *Doctor baseline, <date> (from baseline.md)*, whose
  items are the baselined findings, each **declined**, fingerprinted by the doctor check it names;
  one that names no check is kept with `fingerprint none - converted` and named in the report.

**Each source is copied byte for byte into the upgrade run's `copies/`, at its path from the store,
before it is removed** - the file-facts rule's *move a converted source*. A source is removed only
after its run item is written and its copy is in place; a step stopped between the two leaves the
source, and re-running converts it again into the same run item, found by the title's `from` name.

**Anything in `tmp/` this step does not name is left where it is** and named in the report.
