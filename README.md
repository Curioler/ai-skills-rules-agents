# ai-skills-rules-agents

Skills, agents and templates for **starting** a software project — and for handing each working session cleanly to the next one.

## What this is for

Most of the expensive thinking at the start of a project is the same every time: what to call it, what to build first, which decisions to record, what to leave undecided on purpose, and how to set the repo up so that coding agents do useful work rather than plausible work.

This repo captures that process so it does not have to be re-derived. It came out of one long design conversation for a project called Cairn, and everything in it is grounded in choices that were actually made rather than in general advice.

## What this is *not*

It mostly does not overlap with **`repo-context-sync`** (an Anthropic-bundled skill), which keeps `router.md`, `context.md`, `master_prompt.md` and `testing/testcases.md` current *continuously, while you are writing code*.

The split:

| | Covers | When |
|---|---|---|
| **This repo — `project-bootstrap`** | Naming, stack, scope, milestones, decision records, open questions, glossary, first spec | Before there is code |
| **`repo-context-sync`** | Keeping its own four docs in step, continuously | Throughout, per change |
| **This repo — `session-handoff`** | Draining a session's context into the docs, then writing the next session's opening prompt | At the boundary between sessions |

Use `project-bootstrap` to start, `session-handoff` at each session boundary.

`session-handoff` covers a wider doc set than `repo-context-sync` — the one in `templates/`, including `learning.md`, `decisions/` and `open-questions.md` — and it produces a handoff prompt, which `repo-context-sync` does not. If you run both, `repo-context-sync` is not required to be installed for `session-handoff` to work; neither depends on the other.

## Contents

### Skills

| Skill | Use when |
|---|---|
| [project-bootstrap](skills/project-bootstrap/SKILL.md) | Starting a new project from nothing. The full sequence, in order. |
| [naming-a-project](skills/naming-a-project/SKILL.md) | Choosing a name that becomes the repo, brand, domain and app listing. |
| [solo-with-agents](skills/solo-with-agents/SKILL.md) | The builder is alone, relying on coding agents, possibly new to the stack. |
| [design-records](skills/design-records/SKILL.md) | Setting up decisions, open questions and specs so nothing gets decided by accident. |
| [session-handoff](skills/session-handoff/SKILL.md) | Ending a session, or opening the next one. Updates the docs, then writes the prompt to paste into a fresh session. |

### Agents

| Agent | Does |
|---|---|
| [design-doc-keeper](agents/design-doc-keeper.md) | Brings the design docs back into step after decisions change, and catches status claims that have quietly gone stale. |
| [spec-reviewer](agents/spec-reviewer.md) | Reads a spec cold, hunting placeholders, contradictions and ambiguity. |

### Templates

[templates/](templates/) holds the files a new project starts with — `AGENTS.md`, a `CLAUDE.md` pointing at it, a `.gitattributes`, and a `docs/` tree. They are skeletons with guidance in them, meant to be filled in, not shipped as-is.

## Installing

See [INSTALL.md](INSTALL.md).

## The one idea underneath all of it

**Write down what you decided and why, and write down what you deliberately have not decided.**

A project that records only its decisions accumulates mystery — six months on, nobody remembers whether something was chosen or defaulted into. A project that also records its open questions gets something better: a coding agent that stops and asks instead of quietly picking, which is the difference between a plan you own and one you inherit.

The corollary, and the reason `session-handoff` exists: **a document nobody is told to update does not get updated.** In the project these skills came from, the four docs named as duties in `AGENTS.md` stayed current across 110 commits while the two that were not named — `context.md` and `README.md` — sat untouched, still describing a milestone that had shipped weeks earlier. Every session read them; none had been told to fix them. Assign every doc, or expect the unassigned ones to quietly become the most misleading files you have.
