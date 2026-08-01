---
name: solo-with-agents
description: Use when one person is building a project alone with coding agents doing most of the writing, especially if they are new to the stack. Sets architecture, stack and verification rules that differ from normal defaults. Triggers on "I'm working alone", "I don't know any of these technologies", "I'll build it with you or another coding agent".
---

# Building alone, with agents

This situation inverts several normal defaults. The constraint is not skill or time — it is that **the person responsible for the code cannot fully audit it**. Every rule below follows from that.

## Ask this before advising anything

> Will you need to read this code yourself when something is subtly wrong?

The answer is always yes eventually, and it is the moment that decides the stack.

## Stack rules

**Minimise languages, not lines.** Count the languages a stack commits someone to, not its elegance. One language across web, mobile and shared logic beats a better mobile framework that adds a second. Someone learning from zero has a far gentler ramp with one language, and popular languages have more training data behind them, so agents are more reliable writing them.

**Prefer the boring, popular option.** Agent reliability tracks training data. A well-trodden framework the human can look up beats an elegant one with a small community.

**Prefer managed services over hand-built infrastructure.** A custom backend means owning authentication, migrations, hosting, backups and security — every one of which fails quietly, and every one of which is somewhere an agent can write plausible-looking code that is wrong in a way the human cannot detect by reading it. Managed services collapse most of that into configuration. **Less code exists, so less can be silently wrong.**

**Prefer things whose knowledge transfers.** Postgres over a proprietary document store, when the choice is close. Nothing learned is wasted if the project later moves.

**One repository.** An agent works in one repo per session. Split across repos, it changes an interface in one and cannot see what it breaks in another — and the human becomes the integration layer, which is the job they are least equipped to do.

## Architecture rules

**Isolate the hard logic into something pure.** The genuinely tricky part — the domain rules, the arithmetic, the state machine — goes in its own package with no database, no network, no UI and no clock reads. It takes values in and returns values out.

This is the highest-leverage decision available. Pure logic can be tested by feeding it inputs and checking outputs, which means **correctness is demonstrable to someone who cannot read the implementation**. Everything else follows from protecting that boundary.

**One place talks to the outside world.** One module owns the database client, one owns the API calls. When something is wrong there is one place to look, and if a vendor decision is reversed there is one place to change.

**Views render, they do not compute.** Domain arithmetic inside a UI component is a red flag the human can spot without expertise — which makes it a useful rule precisely because it is checkable.

**Schema lives in migration files, never in a dashboard.** A schema that exists only in a web console is invisible to agents, untestable locally and unrecorded. This applies to every managed backend that offers a click-to-create UI.

**Security is designed in, not hardened later.** Row-level security or its equivalent on every table from the first migration, with tests that attempt unauthorised access and assert failure. For anything holding personal content, a permissions bug does not show a wrong number — it exposes someone's private life.

## Verification rules

This is the section that matters most, and it should go into the project's `AGENTS.md` verbatim.

**Code is verified by running it, not by reading it.** Tests and demonstrable behaviour are the deliverable, not just working code.

**Test names are the specification.** Write them as plain sentences the human can read as a list of guarantees:

- *a parent with two children, one complete, is fifty percent done*
- *a parked child does not count towards its parent's progress*
- *a metric with no readings is zero, not undefined*

If a guarantee the human cares about is not in that list, it is not guaranteed. Reviewing test names is a review they can actually perform.

**Never present a stub, mock or placeholder as finished work.** Say plainly what is incomplete. This is the single most damaging failure mode in this setup, because the human has no way to detect it.

**Never weaken or delete a failing test to make a suite pass.** Fix the cause, or say you could not.

## Working rules

**Explain trade-offs before committing to them, in plain language.** "I chose X over Y because Z" — before, not after.

**Record decisions and non-decisions.** See the **design-records** skill. An open-questions document with a *stop and ask* rule is what prevents an agent quietly resolving something consequential at two in the morning.

**Keep files small and single-purpose.** Agents edit more reliably in focused files, and a human learning the codebase can hold one in their head.

**Say when the human is being asked to decide something.** Flag it, do not bury it in a paragraph. They cannot spot a buried decision the way an experienced developer would.

**Check git history before regenerating a file, never assume it's new.** Before writing a plan, spec or doc to a path that might already exist, run `git log --oneline -- <path>`. A `git status` showing the file as `M` (modified) rather than `??` (untracked) means it is already tracked — read what's there before deciding whether to overwrite it. An agent that skips this can spend a full session silently redoing, or clobbering, work a prior session already committed. Record real instances of this in `docs/learning.md`.

**Two identical infra failures is a signal to stop, not to retry a third time.** Installing tooling, enabling an OS feature, or starting a local service (Docker, WSL2, a database container) either works or hits a wall the agent's sandbox cannot cross — no number of retries fixes a permissions or environment boundary. In particular, anything needing an interactive installer, a restart, or a system feature toggle (WSL2, Hyper-V) needs the human's real desktop session; recognize that fast and hand it off rather than spending turns rediscovering it. Also run anything with a large, low-information log (image pulls, package installs) in the background and read only a tail or targeted grep — reading the full log even once is a real budget cost for zero decision-relevant content, and it compounds across retries. This is the single most expensive failure mode observed in this setup after silent stubs: a session that burns its whole budget on infrastructure setup that was never going to succeed from inside the sandbox, and lands no code.
