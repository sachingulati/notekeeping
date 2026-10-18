# What each finding requires before you may report it

- **1 - merge.** The entries must make *the same claim*, or **each say part of one thing** - not
  merely cover the same topic - in one file or two. **Quote the claims side by side** - the relation
  is the test, so it is shown rather than asserted, in a refusal exactly as in a proposal. **Applying
  a same-claim merge keeps the claim's own words**, with both corrections, and never restates it;
  **applying a part-of-one-thing merge writes one combined entry**, per the promotion rule's
  *Organising as it writes*, and the proposal shows it.
- **2 - split.** The split test in the content tests. **Applying writes the area and its row in `areas/INDEX.md`** - one row, in the index definition's entry format.
- **3 - area.** **Three or more entries on one topic**, across any of the scope's registers, and no
  area for it yet. Quote each entry's claim and the file it sits in, and name the keys the row would
  carry. **Applying moves those entries and writes the row in `areas/INDEX.md`**, as for 2.
  **The topic is judged by what the entries say, never by a shared tag**: `q3` or `urgent` on thirty
  items is a label and has earned nothing. **Count entries, not sources or kinds**: a gotcha, a
  pattern and a runbook step on one topic are three, even when one work item taught two of them -
  whether two entries say the same thing is finding 1's question, never this one's. Two entries are
  not a topic yet - say nothing about them.
- **4 - run the check.** The `Unsettled` test in the content tests, the note being in the form
  promotion gives. **It is a proposal under every flag and is never applied**: report the check
  verbatim and what running it would settle. **A check that ran and disagreed is a different thing entirely** - that is a
  live contradiction, and it goes to a human as-is.
- **5 - retire.** The path must be *gone*, verified against the repository now, not inferred from an
  old entry. **Gone is one answer**: neither `Read` nor `Glob` finds the path in the registered
  repository. Whether it is tracked is not asked. **Never delete**: retirement marks superseded and leaves the text.
- **6 - demote.** The demote test in the content tests.
- **7 - promote.** The promote test in the content tests.
- **8 - flag.** The *Would reopen if* test in the content tests.
  **You never reopen a decision**, and you never write the reversal - both halves stay visible and
  that is a human's call - a ledger records reversals, it does not overwrite them.
- **9 - close.** The newest session block is older than `staleness_warn_days` (the config
  defaults), and no closing block follows it. **A work item explicitly marked open with a stated
  reason is not stale**, however old - that is the `## open <date>` block in its `session.md`, and the
  closing block is its twin. **Both shapes are defined in that file's definition**
  (`session.md`, *Closing, and staying open*):
  one dated heading and one line of reason. **Write that block and nothing else** - `--apply` here
  appends a known shape, never improvised prose, and it never edits what the item already says.
- **10 - demote from `NOTES.md`.** The entry must be genuinely niche. `NOTES.md` is the
  always-loaded file, so this is the one finding where being wrong costs the user on every single
  task; require a clear case and decline the marginal one.
