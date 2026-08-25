# AGENTS.md

<!-- Canonical instruction file. CLAUDE.md should be one line pointing here,
     so switching between coding tools costs nothing. -->

Conventions for any coding agent working in this repo — Claude Code, Cursor, Codex, or anything else. This file is canonical; tool-specific files point here.

## Read before writing any code

1. `docs/context.md` — what this is and where it stands
2. `docs/router.md` — where everything lives
3. `docs/glossary.md` — the vocabulary
4. `docs/plan.md` — the current milestone
5. Any relevant file in `docs/specs/`

## Who you are working with

<!-- Delete whichever does not apply. If the builder is solo and new to the
     stack, keep this section — it is the most load-bearing part of the file. -->

The maintainer is [a solo builder new to this stack, building with coding agents / an experienced developer / a team of N].

Three consequences that matter more than any style rule below:

- **Code is verified by running it, not by reading it.** Tests and demonstrable behaviour are the deliverable, not just working code.
- **Prefer boring and conventional over clever.** A well-trodden solution the maintainer can look up beats an elegant one only you understand.
- **Never present a stub, mock or placeholder as finished work.** If something is incomplete, say so plainly and say what is missing.

Explain significant trade-offs in plain language *before* committing to them.

## Rules that don't bend

<!-- Keep this section verbatim. Every rule here exists because its absence
     cost something real: a decision whose reasoning became unrecoverable, a
     "should work" that didn't, an idea lost because it arrived mid-task. -->

- **A new concept gets a spec before code**, and a decision record with its
  reasoning — not just what was chosen, but what was rejected and why. A record
  that lists only the winner cannot be re-evaluated later.

- **Reversing a decision means appending, never rewriting.** Add a dated
  Addendum that argues whether the old reasoning still holds for the old case or
  was simply overridden. A silent edit makes the original reasoning worthless to
  anyone reading it later — including you, months on.

- **Never claim something works without demonstrating it.** Real test output, a
  real command's real result, a real screenshot. "Should work" is not a result.
  If a tool's own log seems to contradict what you expect, verify independently
  before believing either — a stale process serving old code will happily report
  a healthy status.

- **Never present a stub, mock or placeholder as finished work.** If something is
  incomplete, say so plainly and say what is missing.

- **Capture stray ideas without following them.** Anything that occurs mid-task
  and is not the task goes to `docs/ideas.md` as one line, no design. That is
  what keeps it from being lost *and* from derailing the work in progress.

- **Ask before anything hard to reverse** — production database changes,
  force-pushes, deleting data, publishing to a public domain. Every time, not
  just the first time. Approval in one context does not carry to the next.

## Architecture rules

<!-- These are the rules that stop the codebase degrading. State them as
     absolutes, with the reason — an agent that understands why will apply
     the rule to cases you did not anticipate. -->

- **`packages/[core]` is pure.** No database, no network, no UI, no clock reads. Values in, values out. This is what makes the hard logic testable without running the app.
- **`packages/[data]` is the only place that talks to [the backend].** Nothing else imports the client.
- **Views render and collect input.** They do not compute domain logic.
- **The schema lives in `[migrations/]` as versioned files.** Never change it through a hosted dashboard — a schema that exists only in a web console cannot be reviewed, tested or reproduced.
- **[Private data is private by default.]** Row-level security on every table.

## Testing

- Every rule in `[core]` gets a unit test whose name reads as a plain sentence. The maintainer should understand what the system guarantees by reading the test names alone.
- Access control gets tests that attempt unauthorised reads and assert failure.
- A small set of end-to-end tests covers the main flows.
- Do not delete or weaken a failing test to make a suite pass. Fix the cause, or say you could not.

## Code conventions

- [Language] everywhere. No `any` without a comment saying why.
- [Package manager], never the alternatives.
- Keep files small and single-purpose.
- Name things using `docs/glossary.md`. If you need a concept that is not there, add it in the same change.

## Documentation duties

Update in the same commit:

- New or moved directories → `docs/router.md`
- A significant choice with alternatives → a new file in `docs/decisions/`
- Something you had to decide but were unsure about → `docs/open-questions.md`
- Milestone progress → `docs/plan.md`
- A lesson about working in this repo — a tooling quirk, an environment limitation, a mistake that cost time or budget and how to avoid repeating it → `docs/learning.md`, one dated entry
- An idea that is not the current task → `docs/ideas.md`, one line, no design

## Git

- [Do not commit or push unless asked. / Commit and push when work is complete.]
- Never commit secrets, keys or `.env` files.
- Work on a branch, not directly on the default branch.
