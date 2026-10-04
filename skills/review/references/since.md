# `--since <date>`

**It narrows the *subject*, and it is not free of charge.** The entries added or changed since the
date are what the pass is about. Two things follow, and both must be said out loud rather than
discovered by the user.

**First, what it saves.** Cost falls only where something can be skipped **without reading it**,
and two bounds do that. **The bucket path** - `work/<bucket>/` is the period of first work, so
buckets before the date's period hold no newer work item. **The changed set** - where the store is
inside a repository, git names the files changed since the date: the repo-facts rule's *files
changed since*, run `-C` on the store, kept to paths under it. Only those are read. **Not a
repository, or git unavailable: every file in the window's buckets is read - say so before the
pass.** A register's entries
carry their dates *inside* it, so narrowing to recent entries still costs the whole file. **Say what
was skipped and what still had to be read**, and never report a narrowed pass as though it were
cheap when it was not.

**Second, and this is the sharper half: `--since` changes which findings are possible.** Five of the
ten need more than the subject set, so a narrowed pass **cannot** produce them:

| # | Why a recency filter cannot see it |
|---|---|
| **1 merge** · **7 promote** | both need **a pair**. With one side outside the window there is nothing to compare, and the pass reports *no duplicates* while sitting next to one |
| **2 split** | the threshold is a count of **the whole register**, and a subset cannot exceed a total |
| **3 area** | the test counts **every entry on the topic**, and entries outside the window are not read for it - a topic with one new entry and two old ones is missed |
| **9 close** | **inverted.** It looks for a work item with *no* activity; `--since` selects for activity. A recency filter can never surface it |

**Name the suppressed findings before the pass runs, not in the report afterwards** - a user who
wanted a quick look should learn that half the command is off *while they can still change their
mind*, and a pass that silently drops five findings reports *nothing found* in a store that has
plenty.

**Findings 4, 5, 6, 8 and 10 read one entry at a time and are unaffected.**
