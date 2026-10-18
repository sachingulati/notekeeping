---
doc:   file-facts
title: Reading a file's facts - its size and hash, a text's hash, an exact copy, a move
---

# Reading a file's facts

**A short command answers what `Glob` and `Read` cannot**: a file's size before it is read, whether
it changed, a text's hash, a byte-exact copy, and the one removal the plugin makes. Run each as an ordinary command
in whatever shell you have. **The plugin grants nothing**: whether a call runs quietly, asks, or is
refused is the user's permissions and mode, and a call that asks is let ask - never worked around.

| Fact or act | POSIX shell | PowerShell | Reads as |
|---|---|---|---|
| **size in bytes** | `wc -c <file>...` | `(Get-Item <file>).Length` | one number per file, then `wc`'s `total` line |
| **content hash** | `git hash-object <file>...` | the same | one sha per file, in the order given. **No repository is needed** - it hashes any file |
| **text hash** | `Write` each text to its own file, then `git hash-object <file>...` - one call for them all | the same | one sha per text, in the order given - the *content hash* of a file holding exactly the text. The text is whitespace-collapsed first, per the run-engine rule |
| **an exact copy** | `mkdir -p <dir>`, then `cp <file> <dir>/` - two calls | `New-Item -ItemType Directory -Force <dir>`, then `Copy-Item <file> <dir>` - two calls | the copy, byte for byte - line endings, encoding and trailing space included |
| **move a converted source** | `rm <file>`, after its exact copy | `Remove-Item <file>`, after its exact copy | gone from where it was, kept in the copy. **Only by `/nk:upgrade`, only a source a gather step names, and only once its content is written and its copy is in the upgrade run's `copies/`** - never a store file otherwise, and nothing outside the store |

**Many files in one call.** `wc -c` and `git hash-object` take a list: quote each path, and split a
list too long for one command line into several calls - never `xargs` or `find -exec`, which ask.
**One command per call, run where you stand** - never after a `cd`, never chained with `&&` or
`;` to another. Whether a bare call asks is the user's permissions, but a compound one is judged
as a whole and asks more often, so a chain turns a fact into a prompt and, refused, into *git
unavailable*. **Ask for nothing else of the
shell**: no listing, no search, no edit. `Glob`, `Grep`, `Read`, `Write`
and `Edit` do those on every platform.

## No shell, or a call refused

Each fact has a fallback, and the report says which one ran - never a fallback passed off as the
measurement.

| Fact or act | Without it |
|---|---|
| size | the text as read, rounded - an **estimate**, said so. `/nk:adopt` counts as it reads instead of sizing first, so a run can pass its limit by one file |
| hash | no change detection: a source an earlier adopt run's `inventory.md` records is skipped unless `--again` names it |
| text hash | no fingerprint hash: a finding whose change part is one carries nothing over, and the report says so |
| an exact copy | `Read` the file whole and `Write` it - and the report says the copy **went through the model and may not be byte-exact** |
| move | the source stays where it is, and the report names it: converted, and yours to delete. A re-run converts nothing twice |
