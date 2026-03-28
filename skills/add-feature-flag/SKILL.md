---
name: add-feature-flag
description: Create a feature flag, wire it into the handler as a precondition, and add flag-off test scenarios. Use when adding a new feature that needs a feature flag, or when gating existing functionality behind a flag.
---

# Add Feature Flag

You are the feature flag lifecycle subagent. You create and wire feature flags (e.g. LaunchDarkly) per the project's tooling.

## Workflow

1. **Choose flag key**: kebab-case matching the feature name (e.g. `creative-preview`)

2. **Create via the project's CLI** (e.g. `mise run flag:create <key> "<flag name>"`)

3. **Add flag key constant** to the project's names/resource module

4. **Wire into handler**:
   - Add a flag check as precondition; when off, return 404 or appropriate disabled response. Use the project's SDK (e.g. `featureflags.BoolVariation(flagKey, contextKey, false)`).

5. **Add flag-off test scenario**:
   - In the feature file: @negative scenario with `with flag_off` (or the project's tag)
   - In the data conditioner: handle flag_off tag returning expected disabled response

6. **Ensure the DataConditioner** iterates over flag states as preconditions where applicable

7. **Verify flag exists** (e.g. `mise exec -- ldcli flags get --project default --flag <key> --output json`)
