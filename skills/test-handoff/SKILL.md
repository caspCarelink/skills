---
name: test-handoff
description: >
  Use when the user wants to create a test description/test plan to hand off to a colleague
  for QA, based on implemented Jira tickets and code changes. Triggers on phrases like
  "lav en testbeskrivelse", "test handoff", "lav test til kollega", "test beskrivelse til PR",
  or "/test-handoff [jira-url]".
---

# Test Handoff

Generates a copy-paste-ready Danish test description, formatted to match this repo's PR-description
style, for handing implemented work off to a colleague for testing.

## Usage

```
/test-handoff [jira-ticket-or-url] [base-branch]
```

Both arguments are optional:
- **jira-ticket-or-url** — defaults to inferring the ticket key from the current git branch name
  (e.g. branch `JOTE-5152` → ticket `JOTE-5152`). If no match, ask the user.
- **base-branch** — the branch to diff current work against. **Never assume** this (not even the
  repo's default branch) — always ask the user explicitly if not given as an argument.

## Workflow

### 1. Gather Jira context
- Resolve the ticket key (argument → branch name → ask user, in that order).
- Fetch the ticket via the Jira MCP tools (summary, description, status, issue type).
- Fetch its parent and all sibling subtasks under the same parent (JQL: `parent = <parent-key>`),
  so related subtasks (e.g. a "trigger" subtask and a "visualize" subtask) are included even if the
  user only mentioned the parent story.
- If the ticket can't be found or Jira tools are unavailable, ask the user to paste the description.

### 2. Gather code changes
- Ask the user which branch to diff against.
- Run `git status --short` and `git diff <base-branch>...HEAD` (plus per-file diffs for anything
  still uncommitted) to see everything changed — don't rely on memory of the conversation alone.
- Read the changed files/functions as needed to actually understand behavior, not just the raw diff
  text (e.g. what a new button does, what triggers a chart update).
- Pull in relevant reasoning/decisions from the current conversation session too (rationale, known
  issues discovered, edge cases found during implementation) — the diff alone won't capture *why*.

### 3. Draft "Nødvendig kontekst" (where to find it + config)
- Infer the navigation path (scenes/components/routes) the feature is reachable from, and any
  relevant configuration/feature flags (e.g. `ConfigurationModel` checks, route definitions) from
  the code.
- Present this as a draft and ask the user to confirm or correct it. This section always needs a
  human check — only the user knows the actual click-by-click journey in the browser.

### 3a. Draft "Testopsætning" (always required)
Every handoff needs three concrete setup facts so a colleague can actually log in and reproduce
the feature. Always draft these three, even if the ticket doesn't obviously involve config:

1. **Organisation i Auth-sitet** — the separate Auth application requires picking an organisation/
   tenant before entering Jobmatch. This varies per ticket/feature and can't be inferred from code
   — always ask the user which organisation to select, unless already stated in the conversation.
2. **CPR-nummer til test** — default is `111111-1111`. Use this unless the ticket needs a specific
   citizen/scenario (e.g. a particular age group, målgruppe, or existing data) — ask the user if
   unsure whether the default suffices.
3. **Konfiguration** — if the feature is gated by `ConfigurationModel`/feature flags
   (`configuration?.groups?.*`, `configuration?.properties?.*`), specify exactly which
   properties/groups need to be added, and to which file:
   - Config lives in `src/data/configuration/config_test.json`.
   - Loading it locally requires uncommenting the block in `handleConfigurationData`
     (`src/helpers/ConfigurationHelper.ts`, search for `config_test.json`) — it's wrapped in
     `if (mbitAuthConfig.ClientVersionType === ClientVersionTypes.LOCAL)` so it only takes effect
     on localhost, and is commented out by default so it doesn't affect other environments.
   - Identify the actual property/group keys needed for this ticket by reading the
     `ConfigurationHelper.ts`/`ConfigurationModel` checks the diff touches, so the instruction is
     specific (e.g. "sæt `groups.jobscribe` og `properties.hasJobLog: \"true\"`") rather than generic.
   - If the feature needs no config changes, state that explicitly instead of omitting the section.

### 4. Draft test scenarios
- Self-draft numbered test scenarios covering: happy paths, edge cases visible in the code (empty
  states, error handling, disabled states), and anything explicitly called out in the Jira
  description(s).
- Keep each scenario simple: numbered steps + one **Forventet:** outcome line. Don't invent
  excessive edge cases beyond what the code/tickets actually support.
- Present the full draft once and ask for approval/changes — don't ask step-by-step.

### 5. Draft known limitations
- List any known limitations, edge cases, or explicitly out-of-scope items surfaced by the code,
  diff, or conversation.
- Always ask explicitly: "Er der andre kendte begrænsninger, edge cases, eller ting du bevidst har
  fravalgt, som bør nævnes?" — never assume the draft is complete.

### 6. Ask about anything else missing — one question at a time
Whenever a section can't be confidently filled from Jira/code/conversation, stop and ask the user
**one question at a time**. Look up facts yourself first (git, code, Jira) — only ask the user for
things only they know (intent, product decisions, environment specifics). Never batch multiple
unclear items into a single message.

### 7. Assemble and present

Always output in **Danish**, inside a single fenced ` ```markdown ` code block so it's ready to
copy-paste directly into a PR description:

```markdown
# Testbeskrivelse — <TICKET-KEY>: <kort titel>

## Jira-opgaver
- **[<KEY>](<url>)** — <navn> (<type>, <status>)
  - **[<SUBKEY>](<url>)** — <navn> (<type>, <status>)

## Resumé
<2-4 sætninger: hvad er lavet, og hvorfor>

## Nødvendig kontekst
<Hvor i appen/browseren man finder featuren (klik-for-klik), samt nødvendige konfigurationer/feature flags>

## Testopsætning
- **Organisation (Auth-site):** <organisation der skal vælges ved login>
- **CPR:** <fx 111111-1111, eller specifikt CPR hvis scenariet kræver det>
- **Konfiguration:** <hvilke properties/groups der skal tilføjes til `config_test.json`, samt reminder om at afkommentere config-loading blokken i `ConfigurationHelper.ts` (`handleConfigurationData`) — eller "ingen konfiguration nødvendig">

## Test
### Test 1 — <navn>
1. <trin>
2. <trin>

**Forventet:** <outcome>

(gentag for hvert scenarie)

## Kendte begrænsninger
- <punkt om kendte begrænsninger, ikke-lavede ting, eller acceptable edge cases>
```

Do not save this as a file automatically — only write it to disk if the user separately asks for
that afterward.

## Language rules

Everything in the generated output is always Danish, regardless of what language the user writes
in. Clarifying questions to the user can match whatever language they're using.

## Reflect

**REQUIRED:** After finishing, invoke `reflect` with a one-line summary of this run (what was produced, any corrections made, any friction hit).
