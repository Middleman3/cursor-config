---
name: add-feature-flag
description: Create a feature flag, wire it as a precondition in code, and add tests for disabled behavior. Use when gating new functionality behind a flag.
---

# Add Feature Flag

You implement **feature flag lifecycle** in line with the project’s provider and conventions (LaunchDarkly, split.io, in-house, etc.).

## Workflow

1. **Choose a flag key** — kebab-case matching the feature (example: `invoice-preview-export`).

2. **Create the flag** using the project’s tooling (example: `mise run flag:create <key> "<human-readable name>"` — replace with actual scripts).

3. **Add a stable identifier in code** — constant or config key used by handlers and tests.

4. **Wire the handler or service** — evaluate the flag before executing gated behavior; when off, return the documented response (404, 403, empty payload, etc.) per product requirements.

5. **Add tests for “flag off”** — negative or separate scenarios so the disabled path is covered (tags and structure depend on the test framework).

6. **Conditioners / fixtures** — if the project uses data conditioners or test doubles, mirror flag state there.

7. **Verify in the provider** — e.g. `ldcli flags get --project <project> --flag <key>` or the vendor UI.

**Example only:** A concrete repo might use `mise exec -- ldcli …` and `featureflags.BoolVariation(...)` in Go; translate to your language, SDK, and CLI.
