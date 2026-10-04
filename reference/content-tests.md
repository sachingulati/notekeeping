---
doc: content-tests
title: The content tests
---

# The content tests

Five tests on a store's entries. **Each is stated once, here**; a finding that cites one may not be
reported until its test holds.

- **Split.** Past the threshold **and** a dominant topic. Past the threshold with entries spread
  evenly is not a split proposal - it is a long register, and saying so is the honest finding. Name
  the topic and the count that makes it dominant.
- **Demote.** The entry sits at workspace or global scope and its provenance names exactly **one**
  project. Provenance naming two is correctly placed.
- **Promote.** The **same fact**, not the same subject. Where one project `depends_on` the
  other and the shared fact is the contract between them, it is not a promotion: a contract is read
  from the dependee's code, not kept in the store, so say that and propose nothing.
- **Would reopen if.** The trigger must have *plausibly fired*, and you say what you saw that
  suggests it.
- **`Unsettled`, with a check now cheap to run.** The entry must carry an `Unsettled` note, in the
  form the promotion rules give it. The check is cheap when it is a file to read, a pattern to grep
  or a lookup - not a running environment, a deploy or credentials.
