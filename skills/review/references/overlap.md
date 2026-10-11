# When one entry earns two findings

It must resolve the same way every run. An entry citing a
path that no longer exists, which also makes the same claim as another entry, satisfies **5** and
**1** at once.

**The order is fixed, and it runs from whether the entry should exist to how it should be
arranged:**

| Rank | Findings | The question it answers |
|---|---|---|
| **1** | **5 retire** | should this entry exist at all? Its subject is gone |
| **2** | **6 demote** · **7 promote** · **10 demote from `NOTES.md`** | is it in the right place? |
| **3** | **1 merge** | is it arranged well where it is? |

**The higher rank wins.** Merging two entries about code that no
longer exists is work spent tidying something the next finding proposes to retire; placing an entry
correctly is worth doing before deciding whether it duplicates its new neighbours. **A proposal that
the following proposal would undo is not a finding, it is churn.**

**Report the winner, and name what it displaced, in one clause** - *"retire; this pair would also
merge, which is moot if they go"*. Nothing is lost, the user can see both, and **the count stays the
count** because one entry produced one finding.

**2 split** and **3 area** never compete here: they are findings about a **register**, not about an
entry, so an entry can earn one of those and one of the above without contradiction. **4**, **8** and
**9** are about a contradiction, a decision and a work item respectively, which are not entries in a
register either.
