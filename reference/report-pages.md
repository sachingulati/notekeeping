---
doc:   report-pages
title: Offering a report as a page, and what the page may do
---

# Reports as pages

**Four commands end in a report you have to choose from** - `/nk:adopt`'s trim list, `/nk:doctor`'s
findings, `/nk:upgrade`'s conversion plan, and `/nk:review`'s proposals. **This file is how any of
them offers that report as a page**, and it is the only place the rule is stated. **`/nk:summary`
offers its summary as a page too** - not a report to choose from but a document to share and comment
on, so it keeps the rules below that do not concern choosing, and *A summary as a page* says what differs.

## The terminal report is the product

**Print the report as you always did.** The page is an offer made after it, never instead of it, and
never a thing the user waits for. A command whose output only exists on a page has moved the
product off the terminal, and that is not what this is.

## The offer

**One line, at the very end, and only when there is enough to choose between.** A report with one
finding is not a report to select from; say nothing and stop.

```
That is 14 findings. Want them as a page you can tick through? (y)
```

**Where the report already ends in *apply this?*, the offer is not a second question.** A plain yes
is the go-ahead for the proposal (`report-shape.md`, *A yes applies what was shown*), and two
questions in one turn make that yes ambiguous - measured: `/nk:review` asked both, and a plain yes
got *"reply apply, page, or both"* instead of the write. So word it as a keyword, and put it before
the apply question so the question stays last:

```
That is 14 findings - say "page" to get them as a page you can tick through.
Apply? A plain yes writes findings 1-9 as shown.
```

A plain yes applies and publishes nothing; *"page"* publishes; *"yes and page"* does both.

**`--page` skips the question and goes straight to it**, for anyone who wants that; **`--no-page`
suppresses the offer.** Neither is advertised in the report - a flag only helps someone who already
knows it exists, which is why the question is there.

**Nothing is published until the user asks for it** - a yes to the offer, or "page" where the offer is a keyword. The report is already on their screen; the page
is a second thing, on someone else's infrastructure, and *nothing is created until you ask* is the
promise this design has been strictest about.

## Publishing may not be available, and that is an ordinary outcome

**The tool that publishes a page is not present in every session.** It depends on the account, how
the session signed in, and the organisation's own settings, and **there is no way to ask in
advance.** So:

- **Offer only what you can deliver.** Where publishing is unavailable the attempt fails or writes a
  local file instead; **say so in one line, name the local file if there is one, and stop.** The
  terminal report is untouched and the user has lost nothing.
- **Never make the page a prerequisite.** No command routes a decision through it, and every
  selection the page can express is expressible in the terminal.
- **Never retry, and never offer again in the same run.**

## What the page carries

| | |
|---|---|
| **The findings** | each one as the report printed it - same text, same order, same grouping |
| **Per-item selection** | a control per finding, for the ones this command can act on |
| **A comment box** | per finding, and what is typed there is read exactly as the free text on the write flag is: it narrows and disambiguates within this run's findings, and never reaches past them |
| **Take all proposed** | one control, **behind a two-click confirm** so it cannot overwrite selections already made |

**`take all proposed` and the blanket flag are the same act, and must stay the same act.**
`/nk:review --apply all` *applies every applyable finding in the saved report*, and `/nk:doctor
--fix` takes every unambiguous repair. **If one of the two changes, the other changes with it** -
two surfaces for one authorisation is how they drift.

## What the page never does

- **It never writes.** The page carries a selection; the command performs it. A page that wrote
  directly would put a store edit on a surface the store's own rules do not reach.
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

- **Offer after every write.** A summary has no count to fall short of. Not under `--dry-run`,
  which writes nothing.
- **The page carries the summary as written and nothing else** - its four sections, no controls. Viewers
  comment on it with the page's own comments.
- **Comments are the page's business, not the plugin's.** The plugin never reads a comment and never
  replies to one. Where a comment tags Claude, the platform answers it.
- **The link is kept.** After publishing, write one line `Page: <url>` into `summary.md`, below its
  source line, and nothing else changes. The next `/nk:summary` on that item offers to **update that
  page** rather than make a new one: *"Update the page at `<url>`? (y)"*. If the update fails because
  the page is gone or cannot be reached from this account, say so in one line and offer a new page, which
  replaces the line.
- **`summary.md` is the source, and the page follows it.** A change made on the page and not in
  `summary.md` is replaced by the next update. Say so once when offering to update.

## Under `--oneline`

**No page, and no offer.** An offer is a question with nowhere to go.
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
