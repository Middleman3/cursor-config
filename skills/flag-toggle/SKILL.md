---
name: flag-toggle
description: List, read, and update feature flags using the project’s provider CLI (e.g. LaunchDarkly ldcli)
---

# /flag-toggle

Feature flags are **provider- and project-specific**. Discover how this repo invokes the vendor CLI (often via `mise`, `npx`, or a global binary) and which **project/environment** identifiers apply.

## 1. Discover flags (when key unknown)

Example with **LaunchDarkly** `ldcli` (adjust project key, `--project` value, and wrapper command to match the repo):

```bash
mise exec -- ldcli flags list --project <ld-project-key> --output json
```

## 2. Show current flag state

```bash
mise exec -- ldcli flags get --project <ld-project-key> --flag <flag-key> --output json
```

## 3. Confirm with the user

- Target **environment** (production, staging, etc.)
- Desired **on** or **off**

## 4. Update the flag

```bash
# ON — replace <env-key> with the LaunchDarkly environment key
mise exec -- ldcli flags update --project <ld-project-key> --flag <flag-key> \
  -d '{"environments":{"<env-key>":{"on":true}}}'

# OFF
mise exec -- ldcli flags update --project <ld-project-key> --flag <flag-key> \
  -d '{"environments":{"<env-key>":{"on":false}}}'
```

## 5. Verify and report

- Echo new state from `flags get`.
- Provide a link to the provider’s dashboard for the flag if the user needs UI confirmation.

**Note:** If the project uses a different vendor or HTTP API only, follow its docs and the patterns already in this repository’s scripts or `mise` tasks.
