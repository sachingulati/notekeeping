# Requirements from a file, a paste or a tracker issue

**With a path, read that file. With no value, the requirements are what was pasted into this
session** - take the most recent block of requirement-shaped material. **If there is no such paste,
say so and ask; do not fall back to inference**, because the user has just told you a real source
exists and silently writing a thin file instead looks like it was honoured.

### Distil; do not transcribe

Write the ask, its rationale, its acceptance criteria and its boundaries into the file's format, and
**cite the source** - the path exactly as given, and the date you read it.

- A requirements document that lives in the repository **stays there.** Copying it into the store
  makes a second copy that nothing keeps in sync, and six months later the two disagree with no way
  to tell which was read.
- **The bundle holds the understanding, not an archive.** `requirements.md` is what a cold
  `/nk:load` reads to say what was asked; a document reproduced whole is the thing you had to read
  in the first place.

What the file's own exclusion rule sends elsewhere goes there rather than into the body: how you
will do it -> `plan.md`, what happened and what you learned on the way -> `session.md`.

### Every statement traces to the source, and nothing is added

A document states an ask and a rationale, says nothing about
acceptance criteria, and a plausible set is easy to write - it will read well, and it will be wrong
in a file whose whole value is being evidence of what was *originally* asked.

**Name the absence instead**, under the heading it belongs to: *"the source states no acceptance
criteria."* Never an empty heading, and never a criterion the source does not support.

Say in the report **which source produced the body**, and what the source did not cover.

### It mints, like everything else here

`--requirements` is read when the bundle is created and **never afterwards**. The body is written
once - that is what makes it evidence - and a later, fuller document is an appended amendment rather
than a rewrite. An id that already exists is still a redirect to `/nk:load`, and this flag does not
change that.

**With `--id`, `--requirements` and a fetchable tracker key, both sources are used**: the supplied
document is the body, because handing it over is deliberate, and the issue supplies frontmatter and
is named as the second source. `--dry-run` shows which source would produce the body, and cites it,
without writing.
