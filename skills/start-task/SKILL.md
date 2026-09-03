---
name: start-task
description: >
  Use when the user wants to begin work on a Jira issue by setting up a branch and getting a
  summary of what it asks for. Triggers on phrases like "start på [jira url]", "opret branch til
  [jira url]", "begynd på [jira url]", "hent jira-opgave [url]", or "/start-task [url]". Usually
  invoked as the first step of the ship-feature workflow, but can be used standalone.
---

# Start Task

Sets up everything needed to begin work on a Jira issue: a branch named after the issue key, plus a clear summary of what the issue actually asks for.

## Usage

```
/start-task <jira-url-or-key> [mod <target-branch>]
```

`<target-branch>` defaults to whatever branch is currently checked out.

## Step 1 — Branch setup

- Extract the issue key from the URL/argument (e.g. `JOTE-4155`).
- Determine `<target-branch>`: the `mod <target-branch>` argument if given, otherwise the currently checked-out branch.
- **Warn if it's not a release branch:** feature branches must always be cut from a release branch. If `<target-branch>` does not match `release/Sprint-<number>` (e.g. `release/Sprint-42`), stop and warn: *"Du er ved at oprette branch fra `<target-branch>` — feature-branches bør altid laves fra en release-branch (fx `release/Sprint-42`). Vil du fortsætte alligevel, eller skal jeg skifte til den rigtige release-branch først?"* Wait for confirmation before continuing.
- Once on a confirmed/valid `<target-branch>`, run:
  ```sh
  git fetch origin <target-branch> && \
  git checkout <target-branch> && \
  git pull origin <target-branch> && \
  (git checkout -b <ISSUE-KEY> 2>&1 || git checkout <ISSUE-KEY>) && \
  git branch --show-current
  ```
  (the `checkout -b || checkout` fallback handles the branch already existing, without recreating it)
- Confirm: `Branch \`JOTE-4155\` oprettet og checked ud fra \`<target-branch>\`.`

## Step 2 — Fetch and summarize

- Fetch the issue via the Jira MCP tools.
- Present a structured summary:

```markdown
## Jira: <KEY> — <title>

**Type:** <Bug / Story / Task / ...>
**Prioritet:** <priority>
**Status:** <status>

### Beskrivelse
<condensed to the essential requirements>

### Acceptkriterier
<acceptance criteria, or note that none are defined>

### Yderligere noter
<linked issues, attachments, labels worth noting>
```

- Ask: *"Er der noget i opgaven jeg skal afklare, eller skal jeg gå i gang med implementeringen?"*

## Next steps

From here, implement iteratively with the user (change → test → feedback → repeat) — no fixed recipe for that part. Once the work is approved, invoke `open-pr` to review and open the PR, then `test-handoff` if a QA handoff doc is needed.

## Reflect

**REQUIRED:** After finishing, invoke `reflect` with a one-line summary of this run (what was produced, any corrections made, any friction hit).
