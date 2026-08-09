# Installing

Two ways, depending on whether you want these everywhere or in one project.

## Everywhere (recommended)

Copy the skills and agents into your user-level Claude directory. They become available in every project.

```bash
cp -r skills/* ~/.claude/skills/ && cp -r agents/* ~/.claude/agents/
```

On Windows with PowerShell:

```powershell
Copy-Item -Recurse -Force skills\* $env:USERPROFILE\.claude\skills\; Copy-Item -Recurse -Force agents\* $env:USERPROFILE\.claude\agents\
```

Skills are found by directory name, so `skills/project-bootstrap/SKILL.md` becomes the `project-bootstrap` skill. Agents are found by filename.

## One project only

Copy into the project's `.claude/` directory instead:

```bash
cp -r skills/* /path/to/project/.claude/skills/ && cp -r agents/* /path/to/project/.claude/agents/
```

Useful when a project needs a variant of one of these and you do not want it applied everywhere.

## Starting a new project from the templates

```bash
cp templates/AGENTS.md templates/CLAUDE.md templates/.gitattributes /path/to/new-project/
cp -r templates/docs /path/to/new-project/
```

Then say *"use project-bootstrap"* and work through it. The templates are skeletons — the placeholders are prompts, and a template left unfilled is worse than no template, because it looks like documentation.

## Using them on an existing project

Say *"let's start a new session"* (or *"wrap this up"*) at the end of a working session and `session-handoff` runs: it updates the docs the session invalidated and hands back a prompt to paste into the next one.

It works on any project with a `docs/` tree roughly like `templates/`. It degrades gracefully — files that do not exist are skipped, not created — so it is worth trying before adopting the whole template set.

One thing to do in the project itself: **list every doc in `AGENTS.md`'s documentation duties, including `README.md` and `docs/context.md`.** The skill checks the unassigned files precisely because they are the ones that rot, but a rule in `AGENTS.md` catches them during the session rather than at the end of it.

`.gitattributes` is worth copying on the very first commit rather than later. It normalises line endings, which stops git warning on every commit and — more importantly — stops you eventually seeing a diff where a whole file looks rewritten because its line endings flipped. That is hard to read past when you are trying to check what an agent actually changed.

## Checking it worked

Start a session and ask which skills are available, or type `/` and look for `project-bootstrap` in the list. If a skill does not appear, check that the file is named `SKILL.md` in capitals and that its frontmatter has both `name` and `description`.

## Keeping them current

These came out of one project. The second project will find things that are wrong or missing — a rule that did not survive contact, a template section nobody filled in, a question that should have been asked earlier.

**Edit them when that happens.** A skills repo that never changes after its first use is a snapshot of what someone believed once, which is the least useful thing it could be.
