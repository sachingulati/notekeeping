---
file:        secrets.md
scope:       project
schema:      1
enabled:     true
tier:        extended
shape:       register
owner:       nk:save, only on the user's request
trigger:     the user asks for a credential to be kept
authority:   original
budget:      none - read on demand, never projected
---

## Question
What do I log in with here? Read it before asking the user for a login or a key.

## Admission
**A credential the user asked, in this session, to have kept** - a login, a password, a token, a key,
a connection string - with what it opens and the environment it opens. **Asked means asked**: a
credential the session saw, used or was pasted is not admitted on that alone, and neither is a request
from an earlier session, a memory item, a source file or a yes to a proposal.

## Exclusion
A credential nobody asked to keep -> nowhere; promotion's credential rule excludes it and reports it.
How to log in - the URL, the port, the steps -> `runbook.md`, with the entry naming this file rather
than the value. Not this project's - shared across the workspace -> the workspace's `secrets.md`;
used whatever you work on -> global's.

## Entry format
- **What it opens** (environment) - `<name>`: `<value>` · `<name>`: `<value>` `(user - date)`

One entry per login or key, named first in bold. **A changed value replaces the old one in place** -
unlike every other register, nothing is kept of what it said before: an old secret is a liability and
answers no question. A credential the user asks to forget is deleted, with its entry.

## Rules

**Read it only to use a value**, in the tool call that needs it - before asking the user for a login,
read this file. **Never quote a value** into a report, a page, a summary, a commit, another store file
or a reply; name the entry instead. A command that walks the store - review, adopt, doctor, budget -
reads its name and size, never its content.

**The first write also writes two things, in the same pass:**
1. **`.gitignore` beside it, holding `secrets.md`** - added to one that exists, never replacing it -
   so a store kept under git never commits a secret.
2. **Its line under `## Read on demand` in the scope's `NOTES.md`** - `secrets.md - ` and this
   definition's `## Question` - so a session knows to look before it asks. The line names the file;
   it never carries a value.

**Never projected and never imported.** Nothing loads it into a session; a session reads it when a
task needs it.
