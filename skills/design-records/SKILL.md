---
name: design-records
description: Use when setting up or maintaining a project's decision records, open questions and specs — so choices are recorded with their reasoning and nothing consequential gets decided by accident. Triggers on "why did we choose", "document this decision", "write a spec", "what's still undecided", or an agent about to pick between options with real consequences.
---

# Decisions, open questions and specs

Three documents that work together. Most projects keep the first and skip the second, which is the wrong one to skip.

## Decision records

One file per significant choice, in `docs/decisions/`, numbered and never renamed.

```
0001-name-the-project.md
0002-single-repository.md
0003-typescript-everywhere.md
```

Each contains:

- **Context** — what forced a choice, including what was tried or assumed first
- **Decision** — what was chosen, stated plainly
- **Alternatives considered** — and specifically *why each lost*
- **Consequences** — what follows, including the costs accepted knowingly

**The alternatives section is the point.** Without it, someone proposes a rejected option again in four months and the argument runs from scratch. With it, the answer is a link.

**Record the reasoning, not just the outcome.** "We chose Supabase" is nearly useless. "We chose Supabase because a custom backend is the largest surface for mistakes the maintainer cannot detect by reading code" survives contact with a changed situation, because you can tell whether the reason still holds.

**Superseding.** A decision that turns out wrong gets a new record that supersedes it, and the old one gains a pointer. Do not silently edit history. The exception is a correction made the same day, before anything is built on it — amend in place with a dated note saying what changed and why, because the correction is often more instructive than the original.

## Open questions

`docs/open-questions.md`. The document most projects do not have, and the one that does the most work.

It exists so that **nobody — human or agent — quietly decides something consequential mid-implementation.** State that as a rule at the top of the file:

> **If you need an answer to one of these in order to write code, stop and ask.** Do not pick one and carry on.

Each question gets: what is actually being asked, the candidate answers with what is wrong with each, and a status saying whether it blocks anything.

**Mark what blocks what.** `blocks M1` versus `not blocking` is the difference between a question that halts work and one that can wait.

**Keep genuinely undecidable things open on purpose.** Anything about how an interface feels, or what someone will actually return for, cannot be settled on paper. Say that explicitly, with the reason, so the openness reads as a decision rather than an oversight.

**Answered questions move to a table at the bottom** with a pointer to where they were settled — not deleted. Knowing a question was asked, and where it went, prevents relitigating it.

**Watch for wrongly framed questions.** If every candidate answer is unsatisfying, suspect the question. One project asked "how does a parent's progress derive from its children?" and had four bad answers, because the question assumed all items measure progress the same way. The reframing was worth more than any answer would have been.

## Specs

`docs/specs/YYYY-MM-DD-topic.md`. Dated, never renamed, superseded rather than rewritten.

A spec is where a feature gets argued about while changing it is still free. It is also how someone working with coding agents keeps control of the design: the agent writes the code, the spec decides what the code should do. **Where a spec is vague, the agent fills the gap with its own judgement and the human inherits decisions they never made.**

Contents, each scaled to what it needs:

- **Problem** — what is wrong or missing
- **Scope** — what this covers, and explicitly what it does not
- **Design** — the model, the flow, the interface
- **Decisions** — choices made here, with alternatives, each closing an open question
- **Verification** — how you will know it works, as observable behaviour
- **Open questions this leaves** — cross-referenced

Rules:

- **No `TBD` in a finished spec.** Genuinely undecided things belong in open questions, not as placeholders here.
- **Specify the edge cases.** Division by zero, empty collections, missing values, items excluded from a calculation. If a spec does not say, an agent will decide, and the human will not know a decision was made.
- **If a requirement can be read two ways, pick one and say so.**
- **Once agreed, do not edit a spec to match what got built.** Note the divergence and why. That record is worth more than a tidy document.

Run the **spec-reviewer** agent over a spec before showing it to anyone. It reliably finds things the author cannot see, because the author knows what they meant.

## How the three fit together

```
open question  →  argued in a spec  →  becomes a decision record
      ↑                                          │
      └──────── or stays open on purpose ────────┘
```

A healthy project has all three growing. Decisions only means nobody wrote down what they were unsure about. Open questions only means nothing is getting settled.
