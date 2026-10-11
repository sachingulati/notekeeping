---
file:        domain.md
scope:       project
schema:      1
enabled:     true
shape:       register
owner:       promotion
trigger:     always
authority:   original
budget:      60 entries
---

## Question
What do the words mean here, and what do the models actually represent?

## Admission
A word, status or model whose **real-world meaning** you need to reason about the work, and that
neither its name nor the code states: a term of art, what one record or model stands for in the
world, the lifecycle behind a set of states, a distinction the domain draws that the code splits or
merges.

## Exclusion
Means the same across projects -> the workspace or global.
How it works - a flow, a protocol, authentication, a request -> architecture.md, or gotchas.md where
it surprises. A shape - fields, parameters, an id's format, an endpoint -> read from the code; the
store keeps no contracts. A value - an account, an id, a port, a URL, a setting -> NOTES.md if it is
needed in every session, otherwise nowhere: the config holds it. A name that actively misleads, or
two names that look alike and mean different things -> gotchas.md.

## Entry format
- **Term** - what it means in the world, in a sentence or two. What one record or instance stands
  for, or the lifecycle a set of states walks, where that is the point. Where the code names it, in
  backticks, only when the code's name differs from the domain's.
  > **Corrected <date>:** what the earlier version claimed, and what it cost.

The term first, in bold, as the people in the domain say it - not the identifier, unless the
identifier is the only name it has. That is what keeps a long glossary scannable. A lifecycle is
written as its path, `requested -> accepted | declined`. No entry carries its source or a date, and
none says what to do: a glossary defines, and an instruction or a fix belongs to the file its own
test admits it to. Corrections are inline and never overwrite. About 60 words per entry; longer
means a model or flow is being explained, which is architecture's. No manual numbering.

The rewrite test is this file's whole boundary. An entry here describes the world the code models,
so it stays true if the code is rewritten with every identifier renamed and every endpoint moved.
An entry that would not - how a request is made, what a token is, which field holds an id, what a
value is set to - describes the implementation, and belongs to architecture, gotchas or the code
itself. Without that test anything with an identifier qualifies, and this file becomes a second
gotchas register.
