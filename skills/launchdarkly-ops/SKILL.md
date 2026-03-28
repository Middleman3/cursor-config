---
name: launchdarkly-ops
description: LaunchDarkly feature flags — rollout lifecycle, kill switch via env, relation to deploy canary. Use with /flag-toggle or when wiring LD in application code.
---

# LaunchDarkly operations

## vs deploy canary

- **CodeDeploy / ECS canary:** rolls out **binaries** gradually.
- **LaunchDarkly:** controls **feature exposure** per flag — independent; a full deploy can ship with a flag off for most users.

## Rollout pattern

1. Create flag `feature/name`, default **off**.
2. Merge and deploy (canary or direct per `DANTE_LAUNCH_MODE`).
3. Enable for internal users (e.g. email domain rule).
4. Ramp percentage with monitoring (errors, latency).
5. Remove flag from code and archive in LD when stable.

## Kill switch

- Single env var e.g. `FEATURE_FLAGS_ENABLED=false` to short-circuit SDK and return defaults (document in app `lib/flags.ts` or equivalent).

## CLI / dashboard

- Project uses **ldcli** via mise. If the repo has **cursor-config** (submodule or copy), use the **`/flag-toggle`** command from `commands/flag-toggle.md`: list flags, get state, update environment `on` true/false.

## When to use this skill

- Wiring `@launchdarkly/node-server-sdk` (or client SDKs).
- Coordinating first launch with infra canary and flag rollout docs.
