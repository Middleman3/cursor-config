---
name: prototyping
description: Build frontend UIs with Cradle (service worker + IndexedDB) virtualizing the BFF; wire apps via file package.json deps, VITE_CRADLE flag, and Playwright E2E with E2E_CRADLE for virtual vs real API. Use when vibecoding screens, app prototypes, or BFF spec export from traces.
---

# Prototyping with Cradle

## What

- **Cradle** = same-origin `fetch` → service worker → virtual handlers + IndexedDB; third-party hosts observed and traced.
- **No mock data in components** — use the same API client / `fetch` as production; behavior lives in `cradle.sw.ts` and handlers.

## Wire the app (only `file:` deps)

In the app’s `package.json` (e.g. `software/<app>/`), depend on **local** Cradle packages:

```json
"dependencies": {
  "@cradle/core": "file:../../cradle/packages/core",
  "@cradle/worker": "file:../../cradle/packages/worker",
  "@cradle/vite-plugin": "file:../../cradle/packages/vite-plugin"
},
"devDependencies": {
  "@cradle/spec": "file:../../cradle/packages/spec"
}
```

Adjust `../../` to the path from the app folder to `cradle/packages/*`, then `npm install`. See `software-workflow/cradle/README.md` for Vite + `cradle.sw.ts` steps.

## Virtualization flag

| Env | Effect |
|-----|--------|
| `VITE_CRADLE` unset or ≠ `"0"` | Register SW → **virtual** backend. |
| `VITE_CRADLE=0` | **No** SW → **real** API (network). |

Do not branch feature code on `import.meta.env.DEV` for mocks.

## Playwright E2E

Pass the flag into the **dev server** env so the build matches the mode:

- **`E2E_CRADLE=1`** (or default): set `VITE_CRADLE=1` (or leave unset) so E2E runs with Cradle and proves virtualization works.
- **`E2E_CRADLE=0`**: set `VITE_CRADLE=0` and real API env vars so E2E hits the **real** backend.

Example: `webServer.env` merges `process.env` and `VITE_CRADLE: process.env.E2E_CRADLE === "0" ? "0" : "1"`.

Run both suites in CI when you have both modes.

## Spec export

After flows, export traces → `cradle-export-spec traces.json --out <artifacts-root>` → OpenAPI + `third_party_apis.json` + `bff-backend-prompt.md` under the workflow repo’s `artifacts/`.

## References

- Full detail: see your repo’s Cradle package (example layout: `cradle/README.md` next to the workflow that owns the BFF).
- Submodule propagation: [update-skills](../update-skills/SKILL.md).
