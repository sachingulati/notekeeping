# Global - `~/.notekeeping/`

The fixed home for knowledge that is not about any codebase, and an **ancestor of every workspace**,
which is what makes it reachable everywhere without configuration.

```
~/.notekeeping/
  config.md          schema_version: <the shipped version>
```

**Global's `config.md` is a store config *and* the machine config.** It carries one setting -
`schema_version` - plus the `## Workspaces` registry, which is not a setting. A store
written without `schema_version` has no version at all, and **an absent version is never assumed to
be the current one** - every later upgrade check has to stop and ask.

**Create no knowledge files.** An empty `gotchas.md` is a stub, and a stub is worse than nothing: it
looks answered. Files appear when promotion first writes to them.

**Its path is fixed, so nothing is asked** - say that it was created.

**Creating global writes the global rule file** - `~/.claude/rules/notekeeping.md`, whole, per the
projections rule: the fixed line, then an import of each of global's `NOTES.md`, `instructions.md`
and `environment.md`, **whether or not each exists yet** - a missing import is skipped and loads once
the file is created, so no later write touches the rule file. **Whichever route creates global
writes it** - the automatic form's step 1, or a workspace creation that finds global missing.
