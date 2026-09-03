---
name: add-analytic
description: 'Plan Umami analytics/tracking for a page, scene, or component in the jobportalclient monorepo (jobportal/jobuniverse). Scans the target for trackable elements, grills the user with questions to pin down what matters, then outputs a suggestion list of exactly where to add data-umami-event / trackId / trackUmami() tracking. Use when the user asks to "add tracking", "add analytics", "track this page/scene/component", "umami events for X", or wants a tracking plan/audit. Does not edit files — produces a proposal only.'
---

# Add Analytic (Umami)

Produces a **suggestion list** of exact tracking additions for a page/scene/component. Never edits files — the output is a proposal the user reviews and applies themselves (or asks you to implement afterward as a separate step).

Full API cheatsheet (from Confluence, "Analytic(UMAMI) i jobportal og jobunivers"): [references/umami-api.md](./references/umami-api.md). Read it if you need exact signatures, naming rules, or the click/programmatic decision table — don't re-derive from memory.

## When to Use

- User names a scene/page/component and asks for tracking/analytics on it.
- User asks "where should we track clicks on X" or "audit tracking coverage for X".
- User wants a review of existing `data-umami-event` / `trackUmami` usage on a feature.

## Step 1 — Identify the Target

If the user didn't give an exact file, find it (scene name → `**/scenes/**/*Scene.tsx`, component name → `**/components/**/*.tsx`). Confirm the resolved file path back to the user in your first message rather than asking — this is a fact you can look up, not a decision.

## Step 2 — Scan for Context (before grilling)

Read the target file (and any child components it renders directly) and build a picture of:

- Interactive elements: buttons, links, cards, form fields, dropdowns, accordions, modals triggered from here.
- Existing tracking already present: `data-umami-event="..."`, `trackId="..."` props (e.g. on `GeneralButton`, `Dropdown`), `trackUmami(...)` calls, `AnalyticItem` wrappers.
- Whether this is a **route/page** (needs `useUmamiPageTracking` / `PathMapper` entry) vs a **component** embedded in multiple pages.
- Async flows (API submit, save, error states) — these need `trackUmami()`, not a data attribute.
- Which app it belongs to (`jobportal` or `jobuniverse`) — only matters for which `App.tsx` `pathMappings` array to extend.

Use this scan to make your grill questions concrete (reference actual element names/lines) instead of generic.

## Step 3 — Grill the User

Follow the **grilling** approach: one question at a time, give your recommended answer, wait for confirmation before moving to the next. Skip a question if the scan already answered it unambiguously. Do not finalize the suggestion list until this Q&A is done.

Typical questions, roughly in dependency order:

1. **Goal** — What do you want to learn from this tracking (engagement, drop-off point, feature adoption, conversion)? *Recommend:* infer from page purpose (e.g. a form → drop-off/completion; a list → which items get engagement).
2. **Scope** — Track every clickable element, or only the ones tied to the goal above? *Recommend:* only goal-relevant actions — tagging everything creates dashboard noise.
3. **Page view** — Does this route need a page-view tag via `PathMapper`, or is the default `ROUTE_CHANGED` fine? Are there dynamic segments (ids/guids) in the URL? *Recommend:* add a `PathMapper` entry if the route has dynamic segments or if the raw path is meaningless in the dashboard.
4. **Naming prefix** — Confirm the kebab-case tag prefix (scene/feature name) so all suggested tags stay consistent, e.g. `jobsearch-*`, `cv-*`. *Recommend:* derive from the file/folder name.
5. **Method per element** — For each shared component instance that already exposes `trackId` (`GeneralButton`, `Dropdown`, etc.), confirm using the prop is fine vs a raw `data-umami-event`. *Recommend:* always prefer the existing prop over wrapping in `AnalyticItem` or adding raw attributes.
6. **Extra data / conditional logic** — Any actions needing extra payload data or conditional tracking (e.g. only track submit if validation passed, only track after a successful API response)? *Recommend:* use `trackUmami()` for these, never `data-umami-event`.
7. **Exclusions** — Any elements to explicitly leave untracked (PII fields, sensitive toggles, elements already tracked elsewhere)? *Recommend:* exclude raw PII inputs; never send name/email/CPR as `extra` data.

## Step 4 — Output the Suggestion List

Produce a markdown table, one row per proposed tracking point. Do not write any code changes yet unless the user explicitly asks you to implement afterward.

| Location (file:line) | Element | Suggested Tag (kebab-case) | Method | Action | Notes / Extra Data |
|---|---|---|---|---|---|
| `JobsearchScene.tsx:120` | "Save search" button | `jobsearch-save-search-btn` | `trackId` prop on `GeneralButton` | `click` | — |
| `App.tsx` pathMappings | route `search/detail/entity/id/job` | `full-job` | `PathMapper` entry | `page_viewed` | dynamic `id` segment, type `number` |
| `CVForm.tsx:88` | form submit handler | `cv-form-submitted` | `trackUmami()` | `submit` | extra: `{ section }`, only fire on validation success |

Close with a one-line summary of overall coverage (e.g. "6 new tags proposed, 2 existing `trackId` props reused, 1 new PathMapper entry needed in `jobportal/src/App.tsx`").

## Conventions to Enforce (see reference for details)

- Tags are kebab-case, context-first: `<scene>-<element>-<type>`, never generic (`button-clicked`).
- Prefer `trackId` prop / `data-umami-event` for plain clicks; `trackUmami()` only for extra data, conditional, async, or non-click actions.
- Never put PII (name, email, CPR) in tracked `extra` data.
- New dynamic routes need a `PathMapper` entry in the owning app's `App.tsx`, not a raw path string.

## Reflect

**REQUIRED:** After finishing, invoke `reflect` with a one-line summary of this run (what was produced, any corrections made, any friction hit).
