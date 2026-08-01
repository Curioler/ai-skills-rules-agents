# ai-skills-rules-agents

Skills, agents and templates for **starting** a software project — the phase before any code exists.

## What this is for

Most of the expensive thinking at the start of a project is the same every time: what to call it, what to build first, which decisions to record, what to leave undecided on purpose, and how to set the repo up so that coding agents do useful work rather than plausible work.

This repo captures that process so it does not have to be re-derived. It came out of one long design conversation for a project called Cairn, and everything in it is grounded in choices that were actually made rather than in general advice.

## What this is *not*

It does not overlap with **`repo-context-sync`**, which keeps `router.md`, `context.md` and `master_prompt.md` current *while you are writing code*.

The split:

| | Covers | When |
|---|---|---|
| **This repo** | Naming, stack, scope, milestones, decision records, open questions, glossary, first spec | Before there is code |
| **`repo-context-sync`** | Keeping docs in step with a changing codebase | Once there is code |

Use this to start. Use that to keep going.

## Contents

### Skills

| Skill | Use when |
|---|---|
| [project-bootstrap](skills/project-bootstrap/SKILL.md) | Starting a new project from nothing. The full sequence, in order. |
| [naming-a-project](skills/naming-a-project/SKILL.md) | Choosing a name that becomes the repo, brand, domain and app listing. |
| [solo-with-agents](skills/solo-with-agents/SKILL.md) | The builder is alone, relying on coding agents, possibly new to the stack. |
| [design-records](skills/design-records/SKILL.md) | Setting up decisions, open questions and specs so nothing gets decided by accident. |

### Agents

| Agent | Does |
|---|---|
| [design-doc-keeper](agents/design-doc-keeper.md) | Brings the design docs back into step after decisions change. |
| [spec-reviewer](agents/spec-reviewer.md) | Reads a spec cold, hunting placeholders, contradictions and ambiguity. |

### Templates

[templates/](templates/) holds the files a new project starts with — `AGENTS.md` and a `docs/` tree. They are skeletons with guidance in them, meant to be filled in, not shipped as-is.

## Installing

See [INSTALL.md](INSTALL.md).

## The one idea underneath all of it

**Write down what you decided and why, and write down what you deliberately have not decided.**

A project that records only its decisions accumulates mystery — six months on, nobody remembers whether something was chosen or defaulted into. A project that also records its open questions gets something better: a coding agent that stops and asks instead of quietly picking, which is the difference between a plan you own and one you inherit.
