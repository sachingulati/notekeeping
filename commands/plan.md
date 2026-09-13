---
description: Write plan.md - how the work will be done. Freezes once execution starts.
argument-hint: "[id] [--dry-run] [--caller <name>]"
allowed-tools: Read, Glob, Grep, Write, Edit
---

Fill in `plan.md` for a work item. Writes inside the store only.

**Called by a tool?** If `--caller <name>` is present, follow
`${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`: never ask - refuse naming the argument
that would satisfy it - and end with the outcome line.

Resolve the `plan.md` definition per `${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md` - the
user's overlay wins over the shipped default - and honour its admission and exclusion tests.

## Before writing

Resolve the item, then decide whether the plan is frozen. **The test is not "does `dev.md` have a
session block".**

| State | Do |
|---|---|
| No `plan.md` | **Write the first one**, whatever `dev.md` already holds. Nothing exists to freeze |
| `plan.md` exists, no session block dated on or after it | Rewrite it - it has not been acted on yet |
| `plan.md` exists, and a session block is dated on or after it | **Frozen.** Append a deviation |

Investigating a problem before planning the fix is the commonest case there is, and it leaves a
session block full of findings and no plan. Freezing on that would refuse to write the first plan of
almost every bug.

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
