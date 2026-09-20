---
file:        plan.md
scope:       work
schema:      1
enabled:     true
tier:        extended
shape:       document+append
owner:       nk:plan
trigger:     there is a design decision worth freezing before execution
authority:   original
budget:      none
env_axis:    optional
---

## Question
How will we do it?

## Admission
The approach, the steps, the order, and what each step depends on. The alternatives weighed, where
the choice was close.

## Exclusion
What was asked -> requirements.md. What actually happened -> session.md. A choice the whole project
should inherit -> promote to decisions.md.

## Entry format
Ordinary prose and a numbered list of steps.

**The freeze:** once a session block in session.md is dated **on or after this file**, the plan is no
longer rewritten and a dated deviation is appended instead.

**Not simply "session.md has a session block".** A block recording *what was found* is not execution
against a plan, and a plan that does not exist yet cannot be frozen - a deviation needs a
`**Planned**` line, and there would be nothing to put in it.

So: **no plan.md yet -> write the first one**, whatever session.md already holds. A plan that exists,
with work recorded after it -> append a deviation.

```markdown
---
## Deviation - <date>
**Step**     which step changed
**Planned**  what the plan said
**Actual**   what was done instead
**Because**  what made the difference
```

Plan-versus-actual is the whole value of this file. A plan that is silently updated is always
"correct", and therefore worthless.
