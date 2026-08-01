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
cp templates/AGENTS.md /path/to/new-project/
cp -r templates/docs /path/to/new-project/
```

Then say *"use project-bootstrap"* and work through it. The templates are skeletons — the placeholders are prompts, and a template left unfilled is worse than no template, because it looks like documentation.

## Checking it worked

Start a session and ask which skills are available, or type `/` and look for `project-bootstrap` in the list. If a skill does not appear, check that the file is named `SKILL.md` in capitals and that its frontmatter has both `name` and `description`.

## Keeping them current

These came out of one project. The second project will find things that are wrong or missing — a rule that did not survive contact, a template section nobody filled in, a question that should have been asked earlier.

**Edit them when that happens.** A skills repo that never changes after its first use is a snapshot of what someone believed once, which is the least useful thing it could be.
