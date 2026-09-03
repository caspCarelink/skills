---
name: ship-feature
description: >
  Use when the user gives a Jira URL and wants the full delivery flow handled end-to-end: branch,
  implementation, PR, and handoff. Triggers on phrases like "start på [jira url]", "tag opgaven
  [jira url]", "implement [jira url]", "kør hele flowet på [url]", or "/ship-feature [url]".
---

# Ship Feature

End-to-end delivery: Jira task → branch → implementation → PR → QA handoff. A thin orchestrator — each phase is delegated to its own skill, this one just chains them.

## Invocation

```
/ship-feature <jira-url> [mod <target-branch>]
```

## Flow

1. Invoke `start-task` with the same arguments — creates the branch and presents the Jira summary.
2. Implement iteratively with the user: make changes, present a concise summary of what changed and how to test it, wait for approval or feedback, repeat until approved.
3. Once approved, invoke `open-pr` with `<target-branch>` (and `-config "<message>"` if the feature needs configuration) — reviews the code and opens the PR after you approve the text.
4. Ask if a QA handoff doc is needed; if yes, invoke `test-handoff`.

## Language rules

All communication with the user: Danish. Each sub-skill enforces its own language rules for its artifacts.

## Reflect

**REQUIRED:** After finishing, invoke `reflect` with a one-line summary of this run (what was produced, any corrections made, any friction hit).
