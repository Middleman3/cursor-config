# cursor-config

Centralized Cursor rules, agents, and commands for backend/CDK/Lambda projects. Use as a **submodule** in each repo; point `.cursor` at the submodule so Cursor discovers everything.

## Layout

- **rules/** — `.mdc` rule files (git-workflow, verify-first, backend-conventions, cdk-conventions, test-harness)
- **agents/** — `.md` agent definitions (add-lambda, implement-endpoint, refactor, quality-assurance, etc.)
- **commands/** — `.md` command definitions (commit, pr, cw-logs, check-stacks, flag-toggle, test-feature, gh-logs, slack)

All content on **main** is generic so it applies across projects. Project-specific content (project-overview.mdc, AGENTS.md, project-only commands) lives on **project branches**.

## Branch model

- **main** — All generic rules, agents, and commands. One source of truth.
- **Project branches** (e.g. `marketing-workflow-tools`, `dante-platform`) — Branch from main; add only:
  - `rules/project-overview.mdc`
  - `AGENTS.md`
  - Optional project-only commands (e.g. keep `slack.md` only on branches where the project has `mise run notify:slack`)

## Using in a repo

1. Add as submodule on the project's branch:
   ```bash
   git submodule add -b <branch> https://github.com/<org>/cursor-config.git .cursor-shared
   ```
2. Expose to Cursor:
   - **Symlink:** `ln -snf .cursor-shared .cursor`
   - Or copy `.cursor-shared` → `.cursor` via a script or `mise` task

After cloning a repo that uses this submodule: `git submodule update --init --remote`

## Maintenance

- **Change a rule/agent/command:** Edit on `main`. Merge `main` into project branches; in consuming repos run `git submodule update --remote`.
- **Change project-only content:** Edit on that project's branch only.
