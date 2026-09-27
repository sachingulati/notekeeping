---
description: Write plan.md - how the work will be done. Freezes once execution starts.
argument-hint: "[id] [--dry-run] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

Fill in `plan.md` for a work item. Writes inside the store only.

**`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`.

**Resolve the store first**, per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`, then the item:
`[id]`, or the inference `/nk:save` step 1 makes, resolved against `index.md` - acting on a confident match and asking
where there is none, as `/nk:save` does.

Resolve the `plan.md` definition per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` - the
user's overlay wins over the shipped default - and honour its admission and exclusion tests.

## Before writing

Resolve the item, then decide whether the plan is frozen. **The test is whether a session block is
dated on or after the plan** - not whether `session.md` has one at all.

| State | Do |
|---|---|
| No `plan.md` | **Write the first one**, whatever `session.md` already holds. Nothing exists to freeze |
| `plan.md` exists, no session block dated on or after it | Rewrite it - it has not been acted on yet |
| `plan.md` exists, and a session block is dated on or after it | **Frozen.** Append a deviation |

Investigating before planning the fix leaves a session block full of findings and no plan, which is
the commonest case there is - so the presence of a block cannot be the test.

**When frozen**, do not rewrite. Append a dated deviation instead:

```markdown
---
## Deviation - <date>
**Step**     which step changed
**Planned**  what the plan said
**Actual**   what was done instead
**Because**  what made the difference
```

Plan-versus-actual is the whole value of this file. A plan that is silently updated is always
"correct", and therefore worthless. Say explicitly that you are appending a deviation rather than
editing, so the user is not surprised.

## Writing the plan

Ask only what the session has not already answered. Cover the approach, the steps in order, what each
depends on, and the alternatives weighed where the choice was close.

If a choice here is one the whole project should inherit, say so and offer to promote it to
`decisions.md` - do not quietly write it in two places.

**Never write a stub.** If there is not enough to plan yet, say that and write nothing.

**`--dry-run`** prints what would be written and writes nothing.
