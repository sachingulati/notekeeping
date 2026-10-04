---
doc:   asking
title: How a skill asks, and how an answer it did not pick comes back
---

# Asking

**Every question has one shape**: `nk:<skill> needs:` then the open questions, numbered, each with its
options - *nk:save needs: 1. Which work item is this session about? - 0007 Rate limiting · 0006 Auth
tokens · a new one*. Ask everything still open at once, and nothing the conversation already answers.

- **A person can answer** → ask with `AskUserQuestion`. A pick returns inside this run; carry on with
  everything already settled.
- **The answer comes back as plain text** → this run writes nothing more. **The next action is the
  `Skill` call - `nk:<skill>` with its original arguments**, never with the answer added as one, and
  nothing before it: the skill runs that turn, never the model carrying on from what it remembers of
  the skill. That run infers from the conversation, where the answer now is, and asks only what is
  still open. **A typed *yes* is plain text too**: it re-enters like any other answer. A skill that
  saves a report says what its re-entry reads instead of starting over.
- **Nobody to ask** - a one-turn run, or no question tool → write nothing; end the report with the
  question in this shape, its options, and the argument that answers it where there is one.

**An answer binds the run that asked.** A later run infers fresh. A guess is never an inference, and
a question lost to compaction is asked again.
