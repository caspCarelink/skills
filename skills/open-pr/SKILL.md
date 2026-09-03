---
name: open-pr
description: >
  Use this skill whenever the user wants to prepare, review, or open a pull request (PR). Triggers on phrases like "prepare PR", "open PR", "lav en PR", "opret PR", "make a pull request", "klargør PR", "review kode til PR", or any request to transition code/work to a PR. The skill catches errors, bugs, and misspellings in the code, produces a concise Danish PR description, a one-sentence Danish JIRA release note, and optionally a Danish configuration description if the -config flag is provided — then opens the actual PR once the text is approved.
---

# Open PR

This skill takes a codebase from finished work to an opened pull request. It performs a code review pass to catch issues, generates all the PR artifacts needed — in Danish — then opens the PR itself once you approve the text.

---

## Usage

```
Open PR mod <branch-name> [-config "<configuration description>"]
```

**Required parameter:** `<branch-name>` — The target branch the PR will be merged into (e.g. `main`, `develop`, `staging`).

**Optional flag:** `-config "<message>"` — Use this flag when the feature requires configuration changes (environment variables, feature flags, secrets, infrastructure changes, etc.). Always include a message describing what configuration is needed — the skill will expand and format it into a proper Danish configuration section.

> ⚠️ If `-config` is provided **without** a message, always stop and ask: *"Du har angivet -config — hvad kræver featuren af konfiguration? (fx env-variabler, feature flags, secrets, infrastrukturændringer)"* before proceeding.

---

## Workflow

### Step 1 — Understand the change

Before reviewing, establish what the PR is about:
- If the user has described the feature/fix, use that context.
- If not, ask: *"Hvad handler denne PR om? Giv en kort beskrivelse af ændringen."* (What is this PR about?)
- Check for any files, diffs, or code provided in the conversation. If none, ask the user to paste the relevant diff or changed files.

---

### Step 2 — Code review pass

Carefully review all provided code/diffs and flag issues in these categories. Be thorough but pragmatic — focus on real problems, not nitpicks:

**Errors & bugs**
- Logic errors, off-by-one errors, null/undefined handling, unhandled exceptions
- Incorrect API usage, wrong variable references, missing awaits on async calls
- Security issues (exposed secrets, SQL injection, XSS vectors, missing auth checks)
- Race conditions or incorrect state management

**Code quality**
- Unused imports, variables, or dead code
- Overly complex logic that should be simplified
- Missing or incorrect error handling
- Console.logs or debug statements left in

**Misspellings & typos**
- In variable names, function names, comments, and string literals
- In Danish text strings (UI labels, error messages, etc.)

**Formatting (only if egregious)**
- Inconsistent indentation or obviously wrong formatting

Present findings as a clear, grouped list. For each issue, state:
- **Location** (file + line if known)
- **Problem**
- **Suggested fix**

If no issues are found, say so clearly: *"Koden ser fin ud — ingen åbenlyse fejl fundet."*

---

### Step 3 — Generate PR artifacts

After the review, produce the following. All text must be in **Danish**.

#### PR-beskrivelse (concise, for GitHub/GitLab)

```
## Hvad ændrer denne PR?
<2–4 sætninger der beskriver hvad der er lavet og hvorfor. Undgå teknisk jargon hvor muligt.>

## Ændringer
- <Bullet-punkt liste over de vigtigste ændringer>

## Test
<Kort beskrivelse af hvordan ændringen er testet, eller hvad revieweren bør teste>
```

Keep the PR description concise — aim for clarity over completeness.

#### JIRA Release Note (én sætning)

```
Release note: <Én klar sætning der beskriver hvad der er leveret, skrevet som en funktionel ændring set fra brugerens eller systemets perspektiv.>
```

Write this as a complete sentence in past tense, e.g. *"Tilføjet mulighed for at filtrere søgeresultater på dato."*

#### Konfigurationsbeskrivelse (only if `-config` flag is used)

```
## Konfiguration påkrævet
<Beskrivelse af hvilken konfiguration der er nødvendig for at tage featuren i brug. Inkludér:
- Hvilke miljøvariabler / feature flags / secrets der skal sættes
- Hvor og hvornår de skal sættes (fx før deployment, i Kubernetes secrets, i .env)
- Standardværdier eller eksempelværdier hvor relevant>
```

---

### Step 4 — Present output

Present the review findings first, then the PR artifacts in clearly separated sections. Use code blocks for the artifacts so they're easy to copy.

Ask at the end: *"Er der noget du vil have justeret i beskrivelserne, eller skal jeg oprette PR'en?"*

---

### Step 5 — Create the PR

Only after the user approves the text. Use the GitHub or GitKraken PR-creation tool:

- **title**: `<issue-key>: <short title>` if a Jira key is known from context, otherwise a plain descriptive title
- **body**: the approved PR description from Step 3 (plus config section if present)
- **head**: the current branch
- **base**: `<branch-name>` from the invocation
- **draft**: `false` unless the user asked for a draft

Never call the creation tool before the user has explicitly approved — a plain "ser godt ud" or "opret PR" counts as approval, silence does not.

Confirm afterward:
```
PR oprettet: <PR URL>
```

---

## Example invocation

> Open PR mod develop -config "Feature kræver ny env variabel: FEATURE_FLAG_NEW_SEARCH=true, skal sættes i Kubernetes secrets før deployment"

> Lav en PR mod main — her er diff'en: [paste diff]

---

## Language rules

- All review findings: **Danish or English** (match the language of the codebase/comments)
- PR description, JIRA release note, configuration description: **Always Danish**
- Do NOT use English placeholder text in the Danish artifacts

## Reflect

**REQUIRED:** After finishing, invoke `reflect` with a one-line summary of this run (what was produced, any corrections made, any friction hit).
