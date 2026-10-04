# `set budget`

**A file's budget is not a setting, and `set` writes it anyway.**

**A file's ceiling lives on its definition**, beside the admission test that decides what earns a
place in it - `budget:` in the resolution rule, *What a definition carries*. A number kept away
from its rule drifts from it, and the two are read together or not at all. So the config files
hold no file budgets and `config-defaults.md` lists none.

**`/nk:config set budget <scope>/<file> <value>` sets one**, where `<scope>` is `work`, `project`,
`workspace` or `global` - `set budget project/NOTES.md 14000`. It writes the **overlay**, at
`<store>/schema/files/<scope>/<file>`, because the shipped definition is replaced wholesale on every
plugin update and a number written there would not survive one.

**What it writes depends on what is already at that path**, and there is only ever one file there:

| At `<store>/schema/files/<scope>/<file>` | Write |
|---|---|
| **nothing** | a **fragment** - `extends: shipped` and `budget:`, and nothing else |
| **a fragment** | set `budget:` in it, leaving every other line alone |
| **a whole replacement** (no `extends:`) | set `budget:` in it, and **say that this file is a whole replacement** - the value now sits in a definition that does not inherit, which is the user's own arrangement and not this command's to convert |

**Never create a second overlay for one definition**, and never convert one form to the other. Show
the diff either way, and name the file by absolute path.

**Refuse a budget key whose definition does not resolve**, and refuse one whose resolved definition
carries no `budget:` field **or `budget: none`** - an uncapped file is uncapped by design, and a
ceiling invented for it is a rule nobody wrote. Say which, and stop.

**A budget keeps the unit it is stated in.** A byte ceiling takes bytes; an entry count - `60 entries`
- takes entries; a notice - `notice at 30000 bytes` - takes a new threshold and stays a notice. A
value in another unit is refused, naming the unit. **A budget stated by reference** - an `areas.md`
file keeping the budget of the register it came from - has no value of its own to set: refuse, and
name the register whose budget governs it.
