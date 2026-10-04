---
file:        decisions.md
scope:       project
schema:      1
enabled:     true
tier:        core
shape:       ledger
owner:       promotion
trigger:     always
authority:   original
budget:      none - a ledger is never trimmed
---

## Question
What did we choose, over what, and why?

## Admission
A choice among alternatives that someone will ask about later.

## Exclusion
The practice that resulted -> patterns.md. The trap that resulted -> gotchas.md. A decision nobody
will question -> it fails the gate; it does not belong in the store at all.

## Entry format
**A ledger. Entries are never edited.** A decision that changes gets a new entry that supersedes the
old one by name and date; both stay visible.

```markdown
- **Chose X over Y.** Rejected: Y, Z. Because <the reason that actually decided it>.
  **Would reopen if:** <the condition that would make this wrong>. `(source - date)`
```

**`Would reopen if:` is required.** A decision with no reopening trigger cannot be revisited
rationally - it can only be inherited, or overturned by argument, which is how teams end up
relitigating settled things. It is also mechanically checkable: doctor can surface decisions whose
trigger has plausibly fired.
