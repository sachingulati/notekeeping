---
doc:   tracker
title: The tracker, only through access the session already has
---

# The tracker

**Only where this session already reaches the tracker** - an MCP server the user has connected. The
fetch is theirs, not this plugin's: it configures no tracker and holds no credentials, so where there
is no such tool the step is skipped and the bundle is the source.

**Fetch the issue with its comments and sub-tasks** - the decisive context routinely sits there
rather than in the structured fields.

| Reader | Which ids |
|---|---|
| **resuming an item** | every id in the bundle's `ids:` |
| **minting one** | the resolved id, **only if it matches `tracker_id_pattern`**. The test is on the id, never on the argument: it fires for `--id TKT-482` and not for a key typed as prose |

**A tracker this session cannot reach is named, never guessed.** Say in one line which id was not
fetched and why - no tool for that tracker, or the call failed - and carry on from the bundle. **Never
state an issue's title, status or comments from the id, from memory, or from what such an issue
usually says**: a guessed status reads exactly like a fetched one, and the reader cannot tell them
apart.
