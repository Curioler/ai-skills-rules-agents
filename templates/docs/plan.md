# Development plan

Milestones, in order. This is the todo list for the project.

*Last updated: [date]*

## How this works

- **One milestone at a time.** Nothing from a later milestone gets started early, however tempting.
- **Every milestone gets a spec first**, in `specs/`, agreed before any code is written.
- **Each milestone ends with a "done when"** describing observable behaviour, not finished code. If it cannot be demonstrated, it is not done.
- Milestones beyond the second are sketches. Detail is added as they approach, because living with the first will change what comes after.

---

## M0 — Foundation `in progress`

Getting to the point where building can start.

- [ ] Repo created, empty and private
- [ ] Documentation skeleton
- [ ] [Decisions that block the first spec]
- [ ] Write the M1 spec
- [ ] Scaffold the project
- [ ] CI: type check, lint, tests on every push

**Done when:** tests run green on a clean clone, and something trivial is deployed and reachable.

---

## M1 — [The core thing] `next`

[One line on what this is and why it is first.]

- [ ] [Capability]
- [ ] [Capability]

**Done when:** [observable behaviour, ideally involving a real person doing a real thing for a real length of time].

<!-- A good criterion is falsifiable and slightly uncomfortable. "The
     maintainer replaced their existing tool and did not go back for two
     weeks" is a real test. "The core features are complete" is not. -->

[If something is deliberately in or out of this milestone against expectation, say so here with the reason.]

---

## M2 — Live with it

No new features. Use M1 daily and fix what turns out to be wrong — which will not be what anyone predicted.

**Done when:** [a period has passed and the list of daily annoyances has stopped growing].

---

## M3+ — [Sketches]

[One paragraph each. Say plainly that they are sketches.]

---

## Ordering notes

[Why this sequence. State the principle so that later reordering is a considered change rather than drift — for example: each milestone must be useful on its own to the people already using it, which is why anything needing network effects comes late.]
