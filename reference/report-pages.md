---
doc:   report-pages
title: Offering a report as a page, and what the page may do
---

# Reports as pages

**Four commands end in a report you have to choose from** - `/nk:adopt`'s trim list, `/nk:doctor`'s
findings, `/nk:upgrade`'s conversion plan, and `/nk:review`'s proposals. **This file is how any of
them offers that report as a page.** **`/nk:summary` offers its summary as a page too** - not a
report to choose from but a document to share and comment on, so it keeps the rules below that do not
concern choosing, and *A summary as a page* says what differs.

## The terminal report comes first

**Print the report first; the page is an offer after it** - never instead of it, and never a thing
the user waits for.

## The offer

**One line, at the very end, and only when there is enough to choose between.** A report with one
finding is not a report to select from; say nothing and stop.

```
That is 14 findings. Want them as a page you can tick through? (y)
```

**Where the report ends in the report question, the offer is not a line of its own.** It is that
question's *Publish as a page* (`report-apply.md`, *A yes applies what was shown*): the page goes out
before anything is applied, so it is read and ticked first, and the question comes back for the
decision. *Apply* publishes nothing. Where the offer would not be made - one finding, `--no-page` -
the question has no *Publish as a page*.

**`/nk:adopt` offers it in a question of its own** - its first question's *Review as a page*, beside
*Show here* and its two apply-all picks (its skill) - because its terminal report is the category
summary, not the items.

**`--page` skips the question and goes straight to it**, for anyone who wants that; **`--no-page`
suppresses the offer.** Neither is advertised in the report.

**Nothing is published until the user asks for it** - a yes to the offer, or a pick of *Publish as a
page*. The report is already on their screen; the page is a second thing, on someone else's
infrastructure.

## Publishing may not be available, and that is an ordinary outcome

**The tool that publishes a page is not present in every session.** It depends on the account, how
the session signed in, and the organisation's own settings, and there is no way to ask in advance. So:

- **Offer only when a publishing tool is in your tool list.** If the attempt still fails, or falls
  back to a local file, **say so in one line - naming the run item's `report.html`
  where that is the file - and stop.** The terminal report is untouched and the user has lost
  nothing.
- **Never make the page a prerequisite.** No command routes a decision through it, and every
  selection the page can express is expressible in the terminal.
- **Never retry, and never offer again in the same run.**

## What the page carries

| | |
|---|---|
| **The findings** | each one as the report printed it - same text, same order, same grouping |
| **Per-item selection** | a control per finding, for the ones this command can act on, **kept by the page** so a refresh shows it - *The selection comes back*, below. **`/nk:doctor`'s page carries none** - it has no `--apply`, so its repairs are picked at its report question |
| **Per-category selection** | where the report has lettered categories, one control per category that selects or clears its items; the line below names a whole category by its letter |
| **The `--apply` line** | the exact command for the current selection, shown as text to copy - `/nk:review --apply 0034 3,7,9`. It works in every session, including one that cannot read the page |
| **Take all proposed** | one control, **behind a two-click confirm**, that selects every applyable finding and updates the line above |

**`take all proposed` is the blanket flag's act**: `/nk:review --apply all` applies every
applyable finding in the run's report.

## The selection comes back

**Where the publishing tool can give a page its own stored state, and this session a tool that reads
it back** - on claude.ai, the page's `db` capability and the artifact data tool - the page keeps its
selection there:

- **One document, `selection/current`**, holding `run` - the run item's id - and `items`,
  the numbers and category letters ticked. The page renders from it, so a refresh or another viewer
  sees the same ticks, and writes it on each change. It is the only thing the page writes.
- **After publishing, write `Page: <url>`** into the run's `report.md`, below its header.
- **Read back only on the pick that asks for it** - *Apply the page's selection*, or a typed answer
  naming the page - by getting that one document. **Its `run` must be the open run's id**;
  otherwise say whose selection it is, and apply nothing. An empty or missing selection applies
  nothing and says so.
- **The selection is an answer, never an approval of something new**: it can only name items the
  report holds, and it lands in the items' answers before anything is written - the report-apply rule's
  rules 1-3 apply to it unchanged.

**Without stored state, or without a tool to read it back,** the page builds the `--apply` line
from the ticks it holds in memory, and the report question offers no *Apply the page's selection*.
Say so in one line when publishing: the ticks last until the page is closed.

## What the page never does

- **It never writes the store.** It keeps a selection, and a command performs it - through the
  items' answers in `report.md`, so the run's report stays what is applied. A page that wrote directly would
  put a store edit on a surface the store's own rules do not reach.
- **It never carries more than the report did.** No finding that was not printed, no store content
  the report did not quote, and no path the report did not name.
- **It is not a record.** The store is the record. A page is a view of one run, and a later run
  makes a new one rather than editing it - except a summary's page, which follows `summary.md` and
  is updated in place (*A summary as a page*).

## A summary as a page

`/nk:summary` writes a document meant for people who were not involved, so its page is for reading
and commenting on, not for choosing from. What holds from above: terminal first, one line at the end,
nothing published until asked, `--page` and `--no-page`, and the same one-line failure. What differs:

- **Ask after every write, with `AskUserQuestion`: *Publish / Not now*.** A summary has no count to
  fall short of, and no proposal to apply, so this is its whole question. Not under `--dry-run`,
  which writes nothing, and not under `--no-page`.
- **The page carries the summary as written and nothing else** - its four sections, no controls. Viewers
  comment on it with the page's own comments.
- **Comments are the page's business, not the plugin's.** The plugin never reads a comment and never
  replies to one. Where a comment tags Claude, the platform answers it.
- **The link is kept.** After publishing, write one line `Page: <url>` into `summary.md`, below its
  source line, and nothing else changes. The next `/nk:summary` on that item asks the same question
  with *Publish* meaning **update that page** - *Publish (updates `<url>`) / Not now* - rather than
  make a new one. If the update fails because the page is gone or cannot be reached from this
  account, say so in one line and ask again, *Publish* now making a new page, which replaces the line.
- **`summary.md` is the source, and the page follows it.** A change made on the page and not in
  `summary.md` is replaced by the next update. Say so once when offering to update.
