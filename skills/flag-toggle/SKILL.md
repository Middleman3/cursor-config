---
name: flag-toggle
description: Toggle a LaunchDarkly feature flag on or off
---

# /flag-toggle

1. If no flag key is provided, list available flags:
   ```bash
   mise exec -- ldcli flags list --project default --output json | jq '.[].key'
   ```
2. Show the current state of the flag:
   ```bash
   mise exec -- ldcli flags get --project default --flag <key> --output json
   ```
3. Confirm the desired action with the user (on or off) and which environment (production, staging, etc.)
4. Toggle the flag:
   - Turn ON: `mise exec -- ldcli flags update --project default --flag <key> -d '{"environments":{"<env>":{"on":true}}}'`
   - Turn OFF: `mise exec -- ldcli flags update --project default --flag <key> -d '{"environments":{"<env>":{"on":false}}}'`
5. Verify the new state and report: flag key, previous state, new state, and a link to the LaunchDarkly dashboard for the flag
