# An unregistered repo

**A save resolves the work item's project; it does not ask where you are standing.** One case turns
on the difference: the store resolves, the save is correct, and the repository you are in is not a
registered project of it - so nothing is ever delivered there and nothing ever says why.

**Find the repo root with git** - the repo-facts rule's *the root*, from the working directory.
Resolve that root to a project exactly as the store-resolution rule's *Resolving a project inside the store*
does - its read line first, then the store's `dirs:` entries. **Not a repository, or git
unavailable, say nothing** - you do not know where you are, so never offer to register a
directory you inferred.

**Infer before offering anything.** The working directory is **not** the project: a user can be
standing anywhere and still be working on something registered, and then there is nothing to say.
Resolve the work item's `project:` first, then the repo root by its read line or the registry.
**Only a repo that is none of these is the case this section is about.**

**A repo that resolves by its read line alone is registered** - its notes are delivered and saves
reach them - so nothing here is offered. Where the registry has lost its folder, say the one line
from that rule's *A folder the registry has lost*, and nothing else.

**Then offer what is actually needed**, which is not always registration:

| Where the repo root sits | The offer |
|---|---|
| **inside the workspace root**, unregistered | register it - `/nk:init` from inside it |
| **above the workspace root** | **a project cannot be registered there at all**, so offer to initialise a workspace there, or to point the work item at a project that exists. `/nk:init` would refuse a yes, so never offer one |

> This repo - `/abs/path/web-client` - is not a registered project of this workspace, so notes saved
> here are not delivered to it. Want me to register it? That is `/nk:init` from inside it.

**Ask every time, and record nothing.** A remembered *no* outlives the thing that would have made the
question stop, and a session-scoped memory is no better - it still outlives the fix. Keep it to one
line.

**Never register it yourself.** Initialising is `/nk:init`'s job - it resolves the workspace, refuses
`$HOME`, proposes the name and writes the read line.
