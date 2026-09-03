---
name: plan-sprint
description: >
  Use when the user wants to break a Jira epic or story into smaller sub-tasks with rich,
  codebase-referenced descriptions for sprint planning. Triggers on phrases like "plan sprint",
  "break down epic", "split story into tasks", "lav sprint planning", or "/plan-sprint [jira-url]".
---

# Plan Sprint

Breaks a Jira epic/story into small, actionable sub-tasks with rich Danish descriptions that reference the actual codebase — drafted for approval before anything is created in Jira.

## Usage

```
/plan-sprint [epic-or-story-key-or-url]
```

Run this from within the target project's workspace — codebase references are found by scanning whatever's open, not by asking which repo.

## Workflow

### 1. Fetch the parent

- Resolve the key from the argument, or ask if not given.
- Fetch summary, description, and status via the Jira MCP tools.
- Fetch existing subtasks under it (JQL `parent = <key>`) so the breakdown doesn't duplicate work already split out.

### 2. Scan the codebase

- Search the current workspace for files/components/modules relevant to what the epic/story describes.
- Note existing patterns to follow (similar features already implemented nearby).

### 3. Draft the breakdown

- Split into natural units of work — each a self-contained thing someone could sit down and finish.
- Rule of thumb: no task should take more than **~2 hours**. If a unit is bigger, split it further.
- Order tasks by dependency (what needs to exist before what).

### 4. Write each task description (always Danish)

Use this template per task:

```markdown
## <Kort titel>

**Resumé:** <hvad og hvorfor, 1-3 sætninger>

**Acceptance criteria:**
- <kriterie 1>
- <kriterie 2>

**Kodebase-referencer:**
- `<path/to/file>` — <hvorfor relevant>

**Tekniske noter:**
<tilgang, edge cases, faldgruber>

**Afhængigheder:**
- Parent: <EPIC/STORY-KEY>
- <reference til søskende-task, fx "Kræver output fra Task 2 (kontrakt for X)" eller "Genbrug <ting> fra Task 1">
```

### 5. Present for approval

Show the full draft — all tasks — in one message. Ask the user to approve or request changes. Do not create anything in Jira yet.

### 6. Create in Jira

On approval, create each task as a **Sub-task** under the parent via the Jira MCP tools, in dependency order. Confirm the created issue keys back to the user.

## Reflect

**REQUIRED:** After finishing, invoke `reflect` with a one-line summary of this run (what was produced, any corrections made, any friction hit).
