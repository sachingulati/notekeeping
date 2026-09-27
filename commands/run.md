---
description: Run a command you defined in your store's overlay, or list them. Your own commands, under the store's rules.
argument-hint: "[<name> [args]] [--dry-run] [--oneline]"
allowed-tools: Read, Glob, Grep, Write, Edit, Bash(git rev-parse:*), Bash(git log:*), Bash(git diff:*), Bash(git status:*), Bash(git -C:*)
---

Run one of **your own commands** - a prompt you wrote at `<store>/schema/commands/<name>.md` - or,
with no name, list them. This is how a command you add reaches you: the harness registers only the
plugin's own commands, so yours are run through this one, as `/nk:run <name>`.

**`--oneline`?** Follow `${CLAUDE_PLUGIN_ROOT}/reference/consumer-contract.md`. The authority
to act is the user command's own: `/nk:run` acts normally when the command it runs writes.

## Find the command

Resolve the store per `${CLAUDE_PLUGIN_ROOT}/reference/store-boundary.md`. **Only the resolved
workspace store has an overlay** (`${CLAUDE_PLUGIN_ROOT}/reference/schema/resolution.md`), so
user commands are read from `<store>/schema/commands/` and nowhere else. No store resolved: say so,
and stop.

**No name given: list them.** One line each - the name, and the `description:` from its frontmatter
- then how to run one. None there: say so, and name the directory a command file goes in, and where
the format is documented (`resolution.md`, *A user-defined command*).

**A name given: read `<store>/schema/commands/<name>.md`**; a name containing `/`, `\` or `..` is
refused. Missing: say so and list what does
exist. Never guess a near name and run it.

## Refuse before running

Check the file against the rules in `resolution.md`, *A user-defined command*. **Refuse, name the
rule, and write nothing** when:

1. **`writes:` is absent.** A command that has not said where it writes cannot be held to it.
2. **A `writes:` path resolves outside the store.** Expand it against the store root first. A
   projection, a repository file, anything under `~/.claude/` - all refused.
3. **The name is a shipped command's name** - one of the files in `${CLAUDE_PLUGIN_ROOT}/commands/`.
   `/nk:run save` would read as `/nk:save` and do something else.

## Run it

Follow the command file's body as the instructions for this invocation, with the arguments after
the name as its arguments. **The plugin's rules hold over it wherever they disagree**, as they do
over a prompt overlay: a user command is followed for what to do, and refused for anything a
shipped command could not do either.

- **Before each write, check the target against `writes:`.** A write outside it is not made; say
  which, and continue with the rest only where the command still makes sense without it.
- **A file it writes is resolved through its definition**, if the store defines one - admission,
  exclusion and budget apply exactly as they would to a shipped command writing it.
- **It gets this command's tools and no others.** It can read and search, write and edit, and read
  git. It cannot run any other shell command, and a step that needs one is reported as not done.
  Git only through the read-only subcommands in this file's `allowed-tools` (`rev-parse`, `log`,
  `diff`, `status`), also via `-C`; any other git subcommand is refused and reported as not done.
- **`--dry-run`** prints what it would write and writes nothing - for every user command, whether
  or not its body mentions it.

## Report

Name the command that ran, then **every file written, each checked against `writes:`**, then
anything refused or not done and why. A write that went outside `writes:` despite the check is the
finding to lead with, not bury.
