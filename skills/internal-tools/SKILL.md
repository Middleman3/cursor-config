---
name: internal-tools
description: Set up and maintain static internal_tools in software-workflow (task board, UI artifact viewer) — manifests, S3 config via AWS CLI, INIT.md procedures. Use when initializing internal_tools, changing tasking paths, configuring UI preview buckets, or onboarding agents to follow internal_tools init docs.
---

# Internal tools (software-workflow)

When **internal_tools** under `waypoint-framework/software-workflow/` need setup, updates, or troubleshooting, follow each tool’s **INIT.md** in order:

1. **[waypoint-framework/software-workflow/internal_tools/task-board/INIT.md](../../waypoint-framework/software-workflow/internal_tools/task-board/INIT.md)** — Regenerate `files.json` (`mise run generate-tasking-manifest` from that repo root), keep `config.json` `taskingBase` correct, serve over HTTP to open `task-board/index.html`.
2. **[waypoint-framework/software-workflow/internal_tools/ui-viewer/INIT.md](../../waypoint-framework/software-workflow/internal_tools/ui-viewer/INIT.md)** — Use AWS CLI credentials; run `mise run ui-viewer-sync-config` or `node internal_tools/ui-viewer/sync-config.mjs` from the software-workflow root; ensure the S3 bucket allows the browser to list and GET objects used by the viewer.

Also read **[waypoint-framework/software-workflow/internal_tools/README.md](../../waypoint-framework/software-workflow/internal_tools/README.md)** for the hub layout.

The canonical skill copy also lives in the template at `waypoint-framework/software-workflow/.cursor/skills/internal-tools/SKILL.md`.

**Do not** reintroduce UI forms that ask users to browse for `tasking/` or type bucket names for normal use — paths and bucket selection belong in committed/generated project files and INIT procedures.
