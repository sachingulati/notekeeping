---
doc:   file-facts
title: Reading a file's facts - its size and hash, an exact copy, a removal
---

# Reading a file's facts

**A short command answers what `Glob` and `Read` cannot**: a file's size before it is read, whether
it changed, a byte-exact copy, and the one removal the plugin makes. Run each as an ordinary command
in whatever shell you have. **The plugin grants nothing**: whether a call runs quietly, asks, or is
refused is the user's permissions and mode, and a call that asks is let ask - never worked around.

| Fact or act | POSIX shell | PowerShell | Reads as |
|---|---|---|---|
| **size in bytes** | `wc -c <file>...` | `(Get-Item <file>).Length` | one number per file, then `wc`'s `total` line |
| **content hash** | `git hash-object <file>...` | the same | one sha per file, in the order given. **No repository is needed** - it hashes any file |
| **an exact copy** | `mkdir -p <dir> && cp <file> <dir>/` | `New-Item -ItemType Directory -Force <dir>; Copy-Item <file> <dir>` | the copy, byte for byte - line endings, encoding and trailing space included |
| **remove a saved report** | `rm <file>` | `Remove-Item <file>` | gone. **Only a report under `.notekeeping/tmp/`, and only by `/nk:doctor --fix`** - never a store file, a trim copy, or anything outside the store |

**Many files in one call.** `wc -c` and `git hash-object` take a list: quote each path, and split a
list too long for one command line into several calls - never `xargs` or `find -exec`, which ask.
**Ask for nothing else of the shell**: no listing, no search, no edit. `Glob`, `Grep`, `Read`, `Write`
and `Edit` do those on every platform.

## No shell, or a call refused

Each fact has a fallback, and the report says which one ran - never a fallback passed off as the
measurement.

| Fact or act | Without it |
|---|---|
| size | the text as read, rounded - an **estimate**, said so. `/nk:adopt` counts as it reads instead of sizing first, so a run can pass its limit by one file |
| hash | no change detection: a source `adopted.md` records is skipped unless `--again` names it |
| an exact copy | `Read` the file whole and `Write` it - and the report says the copy **went through the model and may not be byte-exact** |
| remove | write the report empty. **An empty report is a removed one** |
