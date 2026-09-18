---
name: compose-task
description: >
  Use when the user wants to break a Jira epic or story into smaller sub-tasks with rich,
  codebase-referenced descriptions for sprint planning. Triggers on phrases like "plan sprint",
  "break down epic", "split story into tasks", "lav sprint planning", or "/compose-task [jira-url]".
---

# Compose Task

Breaks a Jira epic/story into small, actionable sub-tasks with rich Danish descriptions that reference the actual codebase — drafted for approval before anything is created in Jira.

## Usage

```
/compose-task [epic-or-story-key-or-url]
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

- Favor fewer, chunkier tasks over many tiny ones. Group closely related work (e.g. a component + its styling + its wiring, or a backend endpoint + its validation) into one task rather than splitting each into its own.
- Rule of thumb: a task is roughly **half a day to a day** of work. Only split further if a unit genuinely mixes unrelated concerns or spans multiple layers that different people would own.
- Target **~3-6 sub-tasks** for a typical epic/story. If the draft exceeds that, look for tasks to merge before presenting it.
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

**Labels:** <label1>, <label2>
```

### 5. Assign labels

- Fetch the labels already in use on the project (e.g. via JQL `project = <KEY> ORDER BY created DESC` scanning returned labels, or the parent's own labels) — never invent new ones.
- **Never create a new label.** Only assign labels that already exist in the project. If no existing label fits a task, leave labels empty rather than making one up.
- Pick from the existing set per task and note the choice under **Labels** in each task's description.
- If unsure which existing labels are relevant, ask the user to pick from the fetched list.

### 6. Present for approval

Show the full draft — all tasks — in one message. Ask the user to approve or request changes. Do not create anything in Jira yet.

### 7. Create in Jira

On approval, create each task as a **Sub-task** under the parent via the Jira MCP tools, in dependency order, setting the `labels` field to the reused labels chosen in step 5 (never a new one). Confirm the created issue keys back to the user.

## Reflect

**REQUIRED:** After finishing, invoke `reflect` with a one-line summary of this run (what was produced, any corrections made, any friction hit).
