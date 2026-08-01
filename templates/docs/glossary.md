# Glossary

<!-- Worth writing before the first spec, not after. If the product's model
     is a vocabulary — a hierarchy, a workflow, a set of states — then this
     file IS design work, and skipping it means an agent invents two types
     that mean the same thing. -->

Every term here means exactly one thing, in the code, in the database and in the interface. Use these names. If you need a concept that is not here, add it in the same change that introduces it.

## Core model

**[Term]** — [definition]. [Where relevant, say what it is *not*, and why the distinction matters.]

<!-- State the load-bearing modelling decisions here, not only in a spec.
     For example: "There is deliberately no separate Task type. A task is a
     Goal with no children." That single line prevents a whole class of
     divergence. -->

## [Second grouping — states, progress, lifecycle]

**[Term]** — [definition].

## Product terms

**[Term]** — [definition]. [Whether it is in the current milestone.]

## Words to avoid

<!-- The section that earns this file its place. List the synonyms that have
     been used loosely in conversation and will cause drift if they reach
     the code. Include near-concepts that are genuinely different and must
     not be conflated — naming them here is cheaper than untangling them
     later. -->

- **[Synonym], [synonym], [synonym]** — all mean [canonical term]. Use *[canonical]*.
- **[Term]** — a genuinely different concept ([how it differs]). Not in this product yet. Do not introduce the word until there is a decision to build it.
