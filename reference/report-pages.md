---
doc:   report-pages
title: Offering a report as a page, and what the page may do
---

# Reports as pages

**Four commands end in a report you have to choose from** - `/nk:adopt`'s trim list, `/nk:doctor`'s
findings, `/nk:upgrade`'s conversion plan, and `/nk:review`'s proposals. **This file is how any of
them offers that report as a page**, and it is the only place the rule is stated.

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

**`--page` skips the question and goes straight to it**, for anyone who wants that; **`--no-page`
suppresses the offer.** Neither is advertised in the report - a flag only helps someone who already
knows it exists, which is why the question is there.

**Nothing is published until the user answers yes.** The report is already on their screen; the page
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
`/nk:review --apply all` *authorises every applyable finding this pass produced*, and `/nk:doctor
--fix` takes every unambiguous repair. **If one of the two changes, the other changes with it** -
two surfaces for one authorisation is how they drift.

## What the page never does

- **It never writes.** The page carries a selection; the command performs it. A page that wrote
  directly would put a store edit on a surface the store's own rules do not reach.
- **It never carries more than the report did.** No finding that was not printed, no store content
  the report did not quote, and no path the report did not name.
- **It is not a record.** The store is the record. A page is a view of one run, and a later run
  makes a new one rather than editing it.

## Under `--caller`

**No page, and no offer.** A consumer has no turn to answer in and no browser to open.
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.
