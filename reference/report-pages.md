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
question's *Apply and publish* (`report-shape.md`, *A yes applies what was shown*): one question,
one pick, and the page goes out only on that pick. *Apply* publishes nothing. Where the offer would not be
made - one finding, `--no-page` - the question has no *Apply and publish*.

**`--page` skips the question and goes straight to it**, for anyone who wants that; **`--no-page`
suppresses the offer.** Neither is advertised in the report.

**Nothing is published until the user asks for it** - a yes to the offer, or a pick of *Apply and
publish*. The report is already on their screen; the page is a second thing, on someone else's
infrastructure.

## Publishing may not be available, and that is an ordinary outcome

**The tool that publishes a page is not present in every session.** It depends on the account, how
the session signed in, and the organisation's own settings, and there is no way to ask in advance. So:

- **Offer only when a publishing tool is in your tool list.** If the attempt still fails, or falls
  back to a local file, **say so in one line - naming `.notekeeping/tmp/<command>-<YYYYMMDD>-<n>.html`
  where that is the file - and stop.** The terminal report is untouched and the user has lost
  nothing.
- **Never make the page a prerequisite.** No command routes a decision through it, and every
  selection the page can express is expressible in the terminal.
- **Never retry, and never offer again in the same run.**

## What the page carries

| | |
|---|---|
| **The findings** | each one as the report printed it - same text, same order, same grouping |
| **Per-item selection** | a control per finding, for the ones this command can act on. The page tracks the current selection only to build the line below |
| **The `--apply` line** | the exact command for the current selection, shown as text to copy - `/nk:review --apply review-20260928-2 3,7,9`. Nothing on the page is read back by any command; this line is how a selection reaches one |
| **Take all proposed** | one control, **behind a two-click confirm**, that selects every applyable finding and updates the line above |

**`take all proposed` is the blanket flag's act**: `/nk:review --apply all` applies every
applyable finding in the saved report, and `/nk:doctor --fix` takes every unambiguous repair.

## What the page never does

- **No command reads the page.** The page carries a selection, as the `--apply` line above; the
  user copies it and a command performs it. A page that wrote directly would put a store edit on a
  surface the store's own rules do not reach.
- **It never carries more than the report did.** No finding that was not printed, no store content
  the report did not quote, and no path the report did not name.
- **It is not a record.** The store is the record. A page is a view of one run, and a later run
  makes a new one rather than editing it - except a summary's page, which follows `summary.md` and
  is updated in place (*A summary as a page*).

## A summary as a page

`/nk:summary` writes a document meant for people who were not involved, so its page is for reading
and commenting on, not for choosing from. What holds from above: terminal first, one line at the end,
nothing published until asked, `--page` and `--no-page`, the same one-line failure, and no page under
`--oneline`. What differs:

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

## Under `--oneline`

**No page, and no offer.** An offer is a question with nowhere to go, per the consumer contract.
