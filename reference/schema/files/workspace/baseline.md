---
file:        baseline.md
scope:       workspace
schema:      1
enabled:     true
tier:        extended
shape:       bookkeeping
owner:       nk:doctor
trigger:     /nk:doctor --baseline
authority:   original
budget:      none
---

## Question
Which of doctor's findings has the user acknowledged, so that only new ones are reported?

## Admission
The findings present when `/nk:doctor --baseline` ran, each as doctor reported it - and nothing
added since except by another `--baseline`.

## Exclusion
A finding that appeared after the baseline -> reported as new, never added here quietly. A finding
fixed since -> it simply no longer matches; the line stays until the next `--baseline` rewrites the
file.

## Entry format
Doctor's own report shape, finding by finding, under a header naming the date it was taken - doctor's
*The baseline* section is the one home of it. **A release that changes a finding's shape converts
this file where it can; where it cannot, doctor reports the baseline as unreadable and says to
re-run `--baseline`** - never a silent mismatch that turns every acknowledged finding new.
