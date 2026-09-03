# Umami Tracking API (jobportalclient)

Source: Confluence "Analytic(UMAMI) i jobportal og jobunivers" (space CGID, page 994803713). Condensed for reference — re-fetch the Confluence page via Atlassian MCP if this drifts from reality.

## Two mechanisms

| Mechanism | What | How |
|---|---|---|
| Page tracking | Route change in the SPA | `useUmamiPageTracking` hook (called once per app in `App.tsx`) |
| Event tracking | Click, focus, submit, etc. on a specific element | `data-umami-event` attribute (or `trackId` prop on shared components) **or** `trackUmami()` |

## Script injection

`jobportal/index.html` and `jobuniverse/index.html` each inject the Umami script with their own `data-website-id` and `data-tag={window.location.hostname}` (for environment filtering). Not something you normally touch when adding tracking to a feature.

## Page tracking — `useUmamiPageTracking`

File: `shared/src/hooks/useUmamiPageTracking.ts`. Patches `history.pushState`/`replaceState`, sends a `page_viewed` event on every route change.

Takes an optional `PathMapper[]` to turn a raw path into a readable tag. Without a match, the tag defaults to `'ROUTE_CHANGED'`.

```ts
// jobportal/src/App.tsx
const pathMappings: PathMapper[] = [
    new PathMapper('search/detail/entity/id/job', true, 'full-job', [
        { placeholder: 'id', type: 'number' }
    ]),
    new PathMapper('search', false, 'jobsearch-scene'),
];
useUmamiPageTracking(pathMappings);

// jobuniverse/src/App.tsx
useUmamiPageTracking(); // no mappings yet
```

`PathMapper` (defined in `shared/src/components/Analytics/AnalyticsWrapper.tsx`):

```ts
new PathMapper(
  pathTemplate,  // e.g. 'search/detail/entity/id/job'
  isDynamic,     // true if path has dynamic segments
  tag,           // tag sent to Umami
  replacers?     // [{ placeholder: 'id', type: 'number' | 'guid' | 'string' }]
)
```

## Event tracking

### `data-umami-event` (preferred for plain clicks)

Umami's script auto-listens for clicks on any element with this attribute:

```tsx
<div data-umami-event="jobsearch-jobcard" onClick={...}>...</div>
```

Many shared components expose a `trackId` prop that sets this internally — prefer it over adding a raw attribute:

```tsx
<GeneralButton trackId="save-search-filter-btn" onClick={handleSave}>Gem søgefilter</GeneralButton>
<Dropdown trackId="job-card-dropdown" ...>...</Dropdown>
```

### `trackUmami()` (programmatic — extra data, conditional, async, non-click)

File: `shared/src/components/Analytics/umami.ts`.

```ts
import { trackUmami } from '@shared/components/Analytics/umami';

trackUmami('save-search-filter', 'click', { filterName: 'Min søgning' });
```

Signature:

```ts
trackUmami(tag: string, action: UmamiAction, extra?: Record<string, unknown>): void
```

`UmamiAction`: `'click' | 'focus' | 'blur' | 'submit' | 'page_viewed' | 'time_spent'`.

No-op if `window.umami` isn't available (e.g. in tests) — safe to call unconditionally.

### `prettifyTag`

Formats a kebab/snake/camelCase tag into a readable Umami dashboard name: `'jobsearch-jobcard'` → `'Jobsearch · Jobcard'`. Applied automatically inside `trackUmami`; not something callers invoke directly.

## `data-umami-event` vs `trackUmami()` — decision table

| | `data-umami-event` / `trackId` | `trackUmami()` |
|---|---|---|
| When | Simple click on an element | Complex/conditional logic, non-click events |
| Use case | Buttons, links, cards — one click = one event | Async results, submit flows, `time_spent`, conditional tracking |
| Extra data | No — only the event name | Yes — `extra` object with metadata |
| Action type | Always `click` (auto-detected) | Any: `click`, `submit`, `page_viewed`, `time_spent`, `focus`, `blur` |
| Conditional? | No — always fires on click | Yes — can be wrapped in `if` |
| Import | None — plain HTML attribute | `import { trackUmami } from '@shared/components/Analytics/umami'` |

Rule of thumb: use `data-umami-event`/`trackId` for "user clicked X" with no context needed. Use `trackUmami()` when you need extra data, conditional logic, non-click actions, or tracking after an async result (e.g. after an API response).

## Naming conventions

- kebab-case: `jobsearch-jobcard`, `save-search-filter-btn`
- descriptive and consistent: start with context, end with element type
- avoid generic names (`button-clicked`) — prefer `dismiss-order-btn`

## Verifying tracking works

1. Dashboard: https://analytics.marselisborg.org (needs Umami credentials).
2. To test on localhost: create a personal test website in Umami, copy its Website ID, and temporarily swap `data-website-id` in `jobportal/index.html` / `jobuniverse/index.html` — **never commit that swap**.
3. In the dashboard, check the **Events** or **Realtime** tab after interacting with the element; the tag is prettified (`mit-nye-tracking-id` → `Mit · Nye · Tracking · Id`).
4. To verify the raw request: DevTools → Network → filter `send` → look for a `POST` to `https://analytics.marselisborg.org/api/send` with status `200` and a payload like:

```json
{
    "payload": {
        "website": "...",
        "url": "/aktuel-side",
        "name": "Mit · Nye · Tracking · Id",
        "data": { "tag": "mit-nye-tracking-id", "action": "click" }
    },
    "type": "event"
}
```

Common failure causes: adblocker blocking `window.umami`, empty/missing `data-umami-event`/`trackId`, or the attribute sitting on a wrapper element instead of the actually-clicked one.

## Relevant files

| File | Purpose |
|---|---|
| `shared/src/components/Analytics/umami.ts` | `trackUmami()`, `prettifyTag()`, `UmamiAction` type, `window.umami` type declaration |
| `shared/src/hooks/useUmamiPageTracking.ts` | Hook for automatic page-view tracking on route change |
| `shared/src/components/Analytics/AnalyticsWrapper.tsx` | `PathMapper` class (exported from here) |
| `jobportal/index.html` / `jobuniverse/index.html` | Umami script injection + website ID per app |
| `jobportal/src/App.tsx` / `jobuniverse/src/App.tsx` | Per-app `pathMappings` + `useUmamiPageTracking` call site |
