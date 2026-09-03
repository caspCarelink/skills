---
name: reflect
description: Use immediately after any skill in this repo finishes running, to catch corrections, repeated answers, or friction from that run and log or propose improvements to the skill that just ran.
---

# Reflect

Closes the loop on skills in this repo. Every skill here ends by invoking `reflect` with a short summary of its own run — `reflect` checks that run for signals worth remembering, logs them, and proposes a concrete `SKILL.md` fix when a pattern repeats.

## When Invoked

Called by another skill as its final step, with: which skill just ran, and a one-line summary of what happened (output produced, any corrections, any friction). Never invoked directly by the user.

## Step 1 — Check for signals

Look back over the run for:

- **Corrected output** — user overrode, redid, or rejected something the skill produced.
- **Repeated answer** — user answered a question the same way as a prior run (check that skill's `IMPROVEMENTS.md` history if unsure).
- **Friction** — instructions were ambiguous, wrong, or contradicted the codebase.

No signal found → stop silently, nothing to log.

## Step 2 — Log the signal

Append to `skills/<skill-name>/IMPROVEMENTS.md` (create it if missing):

```markdown
## YYYY-MM-DD
- Signal: corrected-output | repeated-answer | friction
- What happened: <1-2 sentences>
- Suggested fix: <concrete SKILL.md change, or "watching">
```

## Step 3 — Decide if it's strong enough to act on

Read the rest of that skill's `IMPROVEMENTS.md`. Treat it as a **strong signal** if:

- The same issue was already logged once before (this is the 2nd+ occurrence), or
- It's an unambiguous bug/contradiction in the skill text — no need to wait for repeats.

Otherwise (first-time, ambiguous) → log only, stop here.

## Step 4 — Propose the fix

For a strong signal: draft the exact `SKILL.md` edit (old text → new text), show it to the user, and ask to approve/reject. On approval, apply the edit and mark the resolved log entries with `- Resolved: YYYY-MM-DD`.

## Scope

Only for skills stored in this repo (`skills/<name>/`). Skills from other sources aren't touched.
