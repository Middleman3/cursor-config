# cursor-config

Centralized Cursor rules, agents, and commands for backend/CDK/Lambda projects. Use as a **submodule** at `.cursor` in each repo so Cursor discovers everything from that single location.

## Layout

- **rules/** — `.mdc` rule files (git-workflow, verify-first, backend-conventions, cdk-conventions, test-harness, cli-qa-release)
- **skills/** — Agent skills (e.g. cli-qa-release, update-cursor-config)
- **agents/** — `.md` agent definitions (add-lambda, cli-qa-release, implement-endpoint, refactor, quality-assurance, etc.)
- **commands/** — `.md` command definitions (commit, pr, cw-logs, check-stacks, flag-toggle, test-feature, gh-logs, slack)
- **hooks.json** — Cursor hooks config (e.g. stop-hook for QA completion). See [Cursor Hooks](https://cursor.com/docs/hooks). Consuming repos get this when `.cursor` points at this repo; run `git submodule update --remote` to pick up changes.

All content on **main** is generic so it applies across projects. Project-specific content (project-overview.mdc, AGENTS.md, project-only commands) lives on **project branches**.

## Branch model

- **main** — All generic rules, agents, and commands. One source of truth.
- **Project branches** (e.g. `marketing-workflow-tools`, `dante-platform`) — Branch from main; add only:
  - `rules/project-overview.mdc`
  - `AGENTS.md`
  - Optional project-only commands (e.g. keep `slack.md` only on branches where the project has `mise run notify:slack`)

## Using in a repo

Add the submodule at **`.cursor`** so Cursor discovers it directly (no symlink or second folder):

```bash
git submodule add -b <branch> https://github.com/<org>/cursor-config.git .cursor
```

After cloning a repo that uses this submodule: `git submodule update --init --remote`

## Maintenance

- **Change a rule/agent/command:** Edit on `main`. Merge `main` into project branches; in consuming repos run `git submodule update --remote`.
- **Change project-only content:** Edit on that project's branch only.
