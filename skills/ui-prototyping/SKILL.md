---
name: ui-prototyping
description: Prototype product UI in code with the Cradle (service worker + IndexedDB backend virtualization). Forbids inline mock data in components; requires one API path and virtual handlers so disabling Cradle switches cleanly to a real backend. Use when building UI prototypes, vibecoding screens, or generating BFF OpenAPI from traces.
---

# UI prototyping with Cradle

## Goal

Ship **working** frontends (web first; React Native uses a future fetch shim) where **all** backend calls go through the same client as production. **Cradle** virtualizes the backend in-browser (service worker intercepts `fetch`, IndexedDB persists state) so designers and engineers prototype **in the app repo**, not in Figma-only artifacts.

When Cradle is **off** (`VITE_CRADLE=0` or no SW registration), the same UI talks to the **real** API with no rewrites in route files.

## Non-negotiables

1. **No inline mock state in UI** — Do not put `const users = [...]`, `faker`, or MSW handlers inside `pages/`, `routes/`, or feature components. Data loads through the **generated API client** or a single shared `fetch` / `customFetch` wrapper.
2. **Virtual behavior lives in Cradle** — Handlers and config live in `cradle.sw.ts`, `cradle.config.*`, or a dedicated `cradle/` folder in the app, not co-located with presentational components.
3. **Persist like production** — Mutations update **IndexedDB** via Cradle’s store so flows survive refresh where the real backend would persist.
4. **Export the contract** — After meaningful flows, export traces and run the spec pipeline so `bff-from-cradle.json`, `third_party_apis.json`, and `bff-backend-prompt.md` land in the **software-workflow** (or project fork) `artifacts/` tree.

## Forbidden

- Mock arrays/objects in components for “prototype” data.
- `if (import.meta.env.DEV) { return mock }` in feature code.
- MSW or ad-hoc interceptors registered from React tree (Cradle centralizes interception).

## Required workflow

1. **Same-origin API URLs** — Prefer `/api/...` (or your BFF prefix) so the service worker can intercept; use Vite proxy in dev if needed.
2. **Handlers** — Implement `VirtualHandler`s keyed by config (`handlerKey`). Default handler returns 501 until replaced.
3. **Third-party calls** — List external hosts in Cradle config; they are **observed** (passthrough + trace) and rolled into `third_party_apis.json`.
4. **Spec export** — From exported trace JSON: `cradle-export-spec traces.json --out <path-to-artifacts-root>` (see `software-workflow` repo `cradle/README.md`).

## Software-workflow artifacts

| Output | Location |
|--------|----------|
| BFF OpenAPI (from virtual traces) | `artifacts/open_api_specs/bff-from-cradle.json` |
| Third-party manifest | `artifacts/cradle/third_party_apis.json` |
| LLM seed for backend implementation | `artifacts/cradle/bff-backend-prompt.md` |

Use the OpenAPI JSON **plus** the prompt markdown as the **sole** inputs to prompt a backend implementation.

## Trace export (browser)

From a page with an active Cradle service worker:

```ts
const ch = new MessageChannel();
ch.port1.onmessage = (e: MessageEvent) => {
  if (e.data?.type === "CRADLE_TRACES") {
    const json = JSON.stringify(e.data.traces, null, 2);
    // download or copy to traces.json for cradle-export-spec
  }
};
navigator.serviceWorker.controller?.postMessage({ type: "CRADLE_EXPORT_TRACES" }, [ch.port2]);
```

## References

- Cradle packages and Vite setup: `waypoint-framework/software-workflow` → `cradle/README.md` (on branches that include Cradle).
- Propagating Cursor skills: [update-cursor-config](../update-cursor-config/SKILL.md) submodule workflow.
