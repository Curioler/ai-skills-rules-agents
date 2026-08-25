---
name: project-bootstrap
description: Use when starting a new software project from nothing — before any code exists — to name it, choose a stack, decompose the idea into milestones, scaffold the design docs, and write the first spec. Triggers on "I want to build X", "starting a new project", "what repo structure should I use", "help me set up a new app".
---

# Bootstrapping a new project

The sequence below is ordered because each step constrains the next. Choosing a stack before knowing who is building is guesswork; writing a spec before decomposing scope produces a spec for four products at once.

**Do not skip to scaffolding.** The files are the cheap part. The decisions they record are the expensive part.

## The sequence

### 1. Find out who is building this

Ask first, because it changes almost every technical answer:

- Alone, or a team?
- Do they already know the stack, or are they learning it?
- Will coding agents write most of the code?

If the answer is *alone, new to the stack, building with agents*, invoke the **solo-with-agents** skill now and let it set the architecture and verification rules. That situation inverts several defaults — see that skill for why.

### 2. Establish scope, and decompose it if it is too big

Listen for a request that contains several independent products. "A planner, plus social sharing, plus a bucket list, plus web and two mobile apps" is four projects, and refining the details of any one of them is wasted effort until they are separated.

**Say so plainly and early**, then help split it. The ordering rule that matters:

> **Build the part that is useful with one user before the part that needs thousands.**

A social feed is worth nothing with fifty users and depends on the core product existing first. A planner is useful to the person who built it on day one. This ordering also front-loads the differentiator and defers the obligations — moderation, abuse handling, privacy surface — that arrive the moment strangers can post.

### 3. Check whether it already exists

Search properly rather than reasoning from memory; this is a category where knowledge goes stale fast. Look for the product in pieces as well as whole — the useful finding is usually "the parts exist separately and nobody has combined them", which tells you both that the gap is real and roughly why it has survived.

Report the risks you find, including the ones that make the project look worse. A category with many dead attempts is telling you something.

### 4. Name it

Invoke the **naming-a-project** skill. Do this before scaffolding, because the name becomes the repo, the domain, the package, the bundle ID and the store listing.

### 5. Decide the repo structure

Default to **one repository** unless there is a specific reason not to. For agent-assisted work this is close to mandatory: an agent works in one repo per session, so a split means it changes an interface in one place and cannot see the call sites it breaks in another — leaving the human as the integration layer.

Create directories only when something needs them. Empty placeholder folders are guesses. A shared UI package with one consumer is a guess.

Documentation lives with the code. Documents describing a codebase are only true relative to a commit. A separate docs or blueprint repo earns its keep only for material that would survive a full rewrite — product philosophy, roadmap, research — and even then, not on day one.

### 6. Scaffold the docs

Copy from `templates/` and fill in. The set, and why each earns its place:

| File | Why |
|---|---|
| `AGENTS.md` | Conventions every coding agent reads first. `CLAUDE.md` is one line pointing here, so switching tools costs nothing. |
| `docs/context.md` | The short version, for a session starting cold. |
| `docs/router.md` | Index of where everything lives, including what is deliberately absent. |
| `docs/story.md` | Why the project exists. A running narrative, added to over time. |
| `docs/principles.md` | What it is for, so design choices can be checked against something. |
| `docs/glossary.md` | Precise meaning of every domain term, plus words to avoid. |
| `docs/plan.md` | Milestones with observable completion criteria. |
| `docs/open-questions.md` | What is deliberately undecided. |
| `docs/learning.md` | Session/tooling lessons — what wasted budget last time, so the next session doesn't repeat it. Starts empty; fills in as the build hits real friction. |
| `docs/ideas.md` | One line per stray thought that is not the current task, so it is neither lost nor followed. Starts empty. `session-handoff` expects this file to exist. |
| `docs/decisions/` | One record per significant choice, with alternatives. |
| `docs/specs/` | Designs, agreed before they are built. |

Skip `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md` and `CHANGELOG.md` until there are contributors, a public repo or a release. A root `TODO.md` competes with `plan.md`; two lists always diverge.

### 7. Write a glossary before writing a spec

Underrated, and worth doing early when the product's model is a vocabulary — a hierarchy, a workflow, a set of states. Without it, an agent invents two types that mean the same thing and the interface drifts from the code.

Include a **words to avoid** section listing the synonyms that will otherwise leak in.

### 8. Set the milestones

Each milestone needs a **"done when" stated as observable behaviour**, not a list of finished code:

> *Done when the maintainer has moved their real notes into the app and used it for two weeks without going back.*

Detail the first milestone. Sketch the rest, and say plainly that they are sketches — living with the first will change everything after it.

Consider making the second milestone *"use it and fix what is wrong"* with no new features. What breaks is never what anyone predicted.

### 9. Write the first spec

Invoke **design-records** for the conventions. Settle the questions that block it, park the ones that do not, and say which is which.

Then run the **spec-reviewer** agent over it before showing it to anyone.

## Things that go wrong

**Scaffolding before deciding.** Files are cheap and feel like progress. A repo full of well-organised empty documents is not a project.

**Deciding everything.** Some questions genuinely cannot be answered on paper — anything about how a view feels, or what someone will actually return for. Park those explicitly, with the reason, so nobody quietly resolves them mid-implementation.

**Letting personal or sensitive material into the repo.** Real names alongside allegations, medical details, legal strategy, financial specifics. Git history is very hard to scrub, every agent session reads the repo, and one visibility change publishes it permanently. Flag it and make the human decide.

**Accepting the first framing of a question.** If every candidate answer feels unsatisfying, the question is usually wrong. "How does a parent's progress derive from its children?" had four bad answers because it assumed all goals measure progress the same way. The reframing was worth more than any of the answers.
