---
doc:   doctor-fix-text
title: Doctor's --fix <text>, and an answer in words
---

# `--fix <text>`

The flag takes free text, and what the text can do is bounded by the report this run just
printed. It narrows and it disambiguates:

- **Narrow** - *"only the read lines"*, *"skip the ignore entries"*. It picks from the findings in
  front of it.
- **Disambiguate** - supply the judgement a repair was missing, which is what makes a finding that
  was *reported and left alone* repairable in this run.

It cannot reach past the finding list. A repair for something this run did not report is not
authorised by any wording, and neither is anything the four prohibitions forbid - text that asks for
one is refused by name, and the rest of the instruction is still honoured.

The report is the contract: everything this flag does is something the same run printed. Say
which repairs were unambiguous on their own and which the text authorised, as two groups, so the
user can see what their sentence actually bought.
