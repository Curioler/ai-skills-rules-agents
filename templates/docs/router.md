# Router

Index of where everything lives. Update whenever a directory is added or moved.

*Last updated: [date]*

## Start here

| I want to... | Go to |
|---|---|
| Understand what this is, quickly | [context.md](context.md) |
| Know why it exists | [story.md](story.md) |
| Check a design choice against intent | [principles.md](principles.md) |
| Know what a word means | [glossary.md](glossary.md) |
| See what is being built now | [plan.md](plan.md) |
| Know why something was chosen | [decisions/](decisions/) |
| Find what has not been decided | [open-questions.md](open-questions.md) |
| Learn how to work in this repo without repeating a past mistake | [learning.md](learning.md) |
| Park an idea that is not the current task | [ideas.md](ideas.md) |
| Write or follow a design | [specs/](specs/) |
| Work as a coding agent | [../AGENTS.md](../AGENTS.md) |

## Documentation

```
docs/
├── context.md          # the short version
├── router.md           # this file
├── story.md            # why the project exists; a running narrative
├── principles.md       # what it is for; check design decisions against it
├── glossary.md         # precise meaning of every domain term
├── plan.md             # milestones and progress
├── open-questions.md   # deliberately undecided
├── learning.md         # how to work in this repo — session/tooling lessons
├── ideas.md            # stray thoughts, one line each, not yet worth a spec
├── decisions/          # one file per significant choice, with reasoning
└── specs/              # designs, written before they are built
```

## Code

[The tree, with one line per directory saying what belongs there — and, just as usefully, what does not.]

```
[project]/
├── apps/
├── packages/
└── ...
```

## Deliberately absent

<!-- The most useful table here, and the one always left out. Without it,
     every missing directory looks like an oversight and someone helpfully
     creates it. -->

| Path | Waiting for |
|---|---|
| [`apps/mobile/`] | [Milestone N] |
| [`packages/ui/`] | [A second consumer — meaningless with one] |
| `CONTRIBUTING.md` | Contributors |
| `SECURITY.md` | The repo going public |
| `CHANGELOG.md` | A first release |
