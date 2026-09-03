# Skills

Version-controlled custom AI agent skills for work.

## Structure

```
skills/
  <skill-name>/
    SKILL.md          ← skill definition (required)
    references/       ← supporting docs referenced by SKILL.md (optional)
  <category>/          ← optional grouping folder (e.g. utils/)
    <skill-name>/
      SKILL.md
```

## Skills

| Skill | Description |
|---|---|
| [ship-feature](skills/ship-feature/SKILL.md) | Orchestrator: Jira task → branch → implementation → PR → handoff |
| [start-task](skills/start-task/SKILL.md) | Create branch from a Jira issue key and summarize the task |
| [open-pr](skills/open-pr/SKILL.md) | Code review pass + Danish PR description + release note + opens the PR |
| [test-handoff](skills/test-handoff/SKILL.md) | Danish test description for handoff to QA colleague |
| [plan-sprint](skills/plan-sprint/SKILL.md) | Break an epic/story into Danish sub-tasks with codebase references |
| [confluence-documentation](skills/confluence-documentation/SKILL.md) | Danish Confluence docs for features/modules/technical handoffs, picks fixed template variant |
| [add-analytic](skills/utils/add-analytic/SKILL.md) | Umami tracking plan for jobportalclient pages/components |
| [reflect](skills/meta/reflect/SKILL.md) | Logs friction/corrections after a skill runs, proposes fixes when patterns repeat |

## Deploying a skill

Skills are loaded from `~/.agents/skills/<skill-name>/`. To deploy a skill from this repo, copy or symlink it there:

```sh
# copy
cp -r skills/ship-feature ~/.agents/skills/ship-feature

# or symlink (changes here apply immediately)
ln -s "$(pwd)/skills/ship-feature" ~/.agents/skills/ship-feature
```

## Adding a new skill

1. Create `skills/<skill-name>/SKILL.md`
2. End the skill with a `## Reflect` step that invokes the [reflect](skills/meta/reflect/SKILL.md) skill with a one-line run summary
3. Add a row to the table above
4. Commit and push

Skill format: start with a YAML frontmatter block (`name`, `description`), then the skill body in Markdown.

Keep skills small and single-purpose. If a job needs several skills, chain them instead of growing one skill to cover everything.

