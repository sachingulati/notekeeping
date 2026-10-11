---
file:        test.md
scope:       work
schema:      1
enabled:     true
shape:       register
owner:       nk:test
trigger:     verification was designed or run
authority:   original
budget:      none
---

## Question
How is this verified, and what happened when it was?

## Admission
What to exercise, what the correct behaviour is, which surfaces are in scope, what the regression
risk is, and anything that proves the result objectively rather than by eye.

## Exclusion
Steps written for a human to follow by hand -> test-manual.md. How the feature works -> the project's
domain or architecture file. How to run the suite at all -> runbook.md.

## Entry format
- **What is being checked.** How to check it, precisely enough to repeat cold. What the correct
  result is.

Record the outcome of the run that was actually performed, with its date and the environment it ran
in. The environment axis is required here for the same reason it is required on a runbook: a check
passes or fails against a named environment, not in the abstract.
