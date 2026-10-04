---
doc:   overlays
title: Overlays - replacing a skill
---

## A skill overlay

A skill overlay is `<store>/schema/skills/<name>/SKILL.md`; where it exists, follow it instead of the shipped skill. `<store>/schema/skills/<name>/references/<file>.md` replaces that one reference of that skill: where the skill cites its shipped `references/<file>.md`, read the overlay's file of that name instead.

**A skill overlay does not extend**, and `extends:` in one is an error to report. A definition is a
set of named fields and sections, so a part of it can be replaced and the rest inherited with nothing
left ambiguous. Instructions are read in order and depend on each other; merging half of one set into
another produces a prompt nobody wrote and nobody can predict. **Replace the whole thing, or leave
it alone.**

**What an overlay cannot replace:** where the command may write, its `--dry-run` behaviour, the
resolved file definition's admission and exclusion tests, and the promotion rules. Those belong to
the plugin. An overlay that appears to contradict one is followed for style and refused for
substance - say which part you refused, and why.
