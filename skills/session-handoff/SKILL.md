---
name: session-handoff
description: Use when a working session is ending or the next one is being set up — "let's start a new session", "start a fresh chat", "wrap this up", "I'm running low on context" — or before switching to an unrelated area of the project. Also use when the docs look out of step with what the code actually does.
---

# Handing a session off to the next one

A session's most valuable output is usually not the code. It is what the next session would otherwise have to re-derive: what changed, what was decided, what was tried and abandoned, and which file to open first.

That knowledge lives in one place — the current context window — and it is deleted the moment the session ends. **Everything below is about moving it somewhere durable before that happens.**

## The rule that makes this necessary

**A document nobody is told to update does not get updated.**

Observed, not theorised: in a project whose `AGENTS.md` listed milestone progress, decisions, learnings and the router as documentation duties — but not `context.md` or `README.md` — the four named documents stayed current across 110 commits while the two unnamed ones sat untouched, still describing a milestone that had long since shipped. Every session read them. None updated them, because nothing said to.

So this skill checks the files nobody assigned, not just the ones the project's conventions already cover.

## The sequence

### 1. Measure the drift; do not recall it

Memory of what changed is unreliable and, late in a long session, close to worthless. Ask git instead:

```bash
git log --oneline "$(git log -1 --format=%H -- docs/context.md)"..HEAD | wc -l
```

For each doc that claims project state, compare its last-touched commit against `HEAD`:

```bash
for f in README.md docs/context.md docs/router.md docs/plan.md; do
  printf "%-24s %s\n" "$f" "$(git log -1 --format='%ad (%h)' --date=short -- "$f")"
done
```

A doc that has not moved in dozens of commits is not necessarily wrong — a `story.md` or `principles.md` legitimately outlives most changes. A doc that asserts **current state** and has not moved is almost certainly lying.

### 2. Update what this session actually invalidated

**The table below lists default paths — check the project's own `AGENTS.md`/`CLAUDE.md` documentation-duties list first, and use that instead if it differs.** Some projects split part of this out: a separate "blueprint" or "design" repo holding decisions, open questions and a glossary as the source of truth, with the code repo's own `AGENTS.md` explicitly saying it keeps no local copy of those, specifically to avoid drift between two versions of the same record. If that's this project, the equivalent files (and their own naming/numbering convention) live in that other repo, not at the paths below — go there instead of creating a second, competing copy in this one. The underlying duty is the same either way: whatever holds current project state should reflect what this session just did.

Only the live session knows this part. Work the list:

| File | Update when |
|---|---|
| `docs/plan.md` | A milestone item got done, changed, or was added |
| `docs/learning.md` | A tooling quirk, environment wall, or mistake cost real time — one dated entry |
| `docs/router.md` | A directory or significant file was added, moved or removed |
| `docs/decisions/` | A choice with real alternatives got made |
| `docs/open-questions.md` | A question was settled (move it to Closed, with a pointer) or a new one opened |
| `docs/ideas.md` | Something worth doing surfaced that was not the task at hand |
| `docs/specs/` | The thing built diverged from its spec — record the divergence, do not quietly rewrite history |
| `docs/glossary.md` | A new domain term entered the code |
| **`docs/context.md`** | **Any of the above changed the project's current state.** The most-skipped file |
| **`README.md`** | **Status, test counts, structure or stack changed.** The second-most-skipped |

**Status claims are the failure point.** "M0 in progress", "no UI yet", "67 tests" are assertions with a shelf life. Grep for them and check each against reality:

```bash
grep -rn "in progress\|not yet\|no database\|coming soon\|[0-9]\+ tests" README.md docs/context.md
```

Verify counts rather than adjusting them by feel — run the suite and read the number.

### 3. Sweep for cross-file contradictions

Dispatch the **design-doc-keeper** agent. It reads cold, which is the point: it catches the contradictions a session has gone blind to, and it has no stake in what was just written.

It reports what it changed and — more usefully — what it noticed but could not resolve. Read that second list.

### 4. Ask what the next session is for

**Ask before writing the prompt.** Do not guess, and do not assume it continues this session's work — the most common reason for a fresh session is precisely that the subject is changing.

Then scope the reading list to that answer. A prompt that tells the next agent to read eleven documents when three are relevant burns context on orientation that buys nothing.

### 5. Write the init prompt

Emit it in one fenced block, ready to copy whole. It must stand alone: the next session shares no memory with this one.

```markdown
Continue [PROJECT] — read [the 2-4 files this task actually needs] to orient.
Check `git log --oneline -10` for what landed recently.

## Where things stand
[2-3 sentences: current milestone, what just shipped, what is verified vs. assumed.]

## This session's task
[What the user said they want to do, in their terms.]

## What you need to know that is not in the docs
[Environment state — services that must be started, what is deliberately switched off,
credentials that exist, verification steps that cannot run here. The tacit knowledge.]

## Constraints
[Standing rules that bite on this task — from AGENTS.md and from what the user has
corrected before.]
```

**Include what is unverified.** "The e2e suite passed; the mobile layout was never looked at in a browser" is worth more to the next session than any summary of what was built. Carrying a false sense of doneness forward is the expensive failure.

### 6. Propose before changing a skill or an agent

If the handoff surfaces a fix belonging in a skill or agent rather than in the project — a rule that failed, a step nobody follows, a missing trigger — **say what you would change and wait.** Do not edit it as a side effect of tidying up.

Reusable instructions are leverage: a wrong one repeats across every project that installs it, silently, long after the session that introduced it is forgotten. That asymmetry is why they get a confirmation step when project files do not.

### 7. Publish anything reusable

A skill improved in one project and left there helps nobody twice. Commit and push changed skills and agents to the shared repo, then reinstall so the local copy matches what was published:

```bash
cp -r skills/* ~/.claude/skills/ && cp -r agents/* ~/.claude/agents/
```

## Things that go wrong

**Writing the summary from memory at the end of a long session.** Late-session recall is confidently wrong about which of several similar changes actually landed. Read `git log` and `git diff --stat`; they do not misremember.

**Updating `plan.md` and stopping.** It is the satisfying one — boxes get ticked. It is also the one most projects already have a rule for, which means it is the least likely to be the stale one.

**Marking work done that was only written.** Typechecking is not testing, tests passing is not the feature working, and neither is a browser look. Record which of those actually happened. An honest "built, not verified" is more useful than a confident "done".

**Letting the init prompt inherit this session's framing.** The next session is not a continuation unless the user says it is. Ask.

**Treating a long doc list as thoroughness.** Every file the next session is told to read costs context before any work starts. Name the ones that change what the agent will do.
