# Specs

Designs, written and agreed **before** anything is built.

## Why this exists

A spec is where a feature gets argued about while changing it is still free. Once code exists, every disagreement costs a rewrite.

It is also how someone working with coding agents keeps control of the design. The agent writes the code; the spec decides what the code should do. **Where the spec is vague, the agent fills the gap with its own judgement, and you inherit decisions you never made.**

## Naming

```
YYYY-MM-DD-topic.md
```

Dated, so the order is obvious. Never renamed — a superseded spec says so at the top and links forward.

## What a spec contains

Scale each section to what it needs. A short spec that is precise beats a long one that hedges.

- **Problem** — what is wrong or missing today
- **Scope** — what this covers, and explicitly what it does not
- **Design** — the model, the flow, the interface
- **Decisions** — choices made here, with alternatives, each closing an open question
- **Verification** — how you will know it works, as behaviour you can observe
- **Open questions this leaves** — cross-referenced to `../open-questions.md`

## Rules

- **No `TBD` in a finished spec.** Genuinely undecided things belong in open questions, not as placeholders here.
- **Specify the edge cases.** Empty collections, division by zero, missing values, boundaries, items excluded from a calculation. If the spec does not say, an agent decides, and nobody knows a decision was made.
- **If a requirement can be read two ways, pick one and say so.**
- **Use the glossary.** A spec needing a new term adds it to the glossary in the same change.
- **Once agreed, do not edit a spec to match what got built.** Note the divergence and why — that record is worth more than a tidy document.

Run the `spec-reviewer` agent over a spec before showing it to anyone.

## Index

| Spec | Covers | Status |
|---|---|---|
| | | |
