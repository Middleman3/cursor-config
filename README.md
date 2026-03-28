# cursor-config

Cursor configuration and **Agent Skills** for backend/CDK/Lambda projects. Compatible with the [Agent Skills standard](https://agentskills.io) and the [skills CLI](https://skills.sh/docs/cli) (e.g. Vercel skills).

**Workspace map:** For an outline of all repos in the Current folder (cursor-config, dante-platform, test-harness, waypoint-framework, waypoint-projects, docs) and what each is used for, see [workspace-repos.md](workspace-repos.md).

## Two ways to use this repo

### 1. Install skills only (skills CLI / Agent Skills standard)

Install skills into your project with the standard CLI. Skills are discovered from the **`skills/`** directory.

```bash
# From GitHub (replace <org> with your org)
npx skills add <org>/cursor-config

# List skills without installing
npx skills add <org>/cursor-config --list

# Install specific skills
npx skills add <org>/cursor-config --skill cli-qa-release --skill update-cursor-config

# From a local path
npx skills add ./path/to/cursor-config
```

The CLI installs into your agent’s skill directory (e.g. for Cursor: `.agents/skills/` at project scope, or `~/.cursor/skills/` globally with `-g`). Only the contents of **`skills/`** are used; rules, agents, and commands in this repo are not installed by the CLI.

### 2. Full Cursor config (submodule)

Use this repo as a **git submodule** at **`.cursor`** so Cursor discovers rules, agents, commands, hooks, and skills from one place.

```bash
git submodule add -b <branch> https://github.com/<org>/cursor-config.git .cursor
```

After cloning a repo that uses this submodule: `git submodule update --init --remote`

## Layout

| Path | Purpose | Used by |
|------|---------|--------|
| **skills/** | Agent skills (each `*/SKILL.md` with `name` + `description` frontmatter). [Standard locations](https://cursor.com/docs/skills) for Cursor: `.cursor/skills/`, `.agents/skills/`. | skills CLI, Cursor (submodule) |
| **rules/** | `.mdc` rule files (git-workflow, verify-first, backend-conventions, cdk-conventions, test-harness, cli-qa-release) | Cursor only (submodule) |
| **agents/** | `.md` agent definitions (add-lambda, implement-endpoint, refactor, quality-assurance, etc.) | Cursor only (submodule) |
| **commands/** | `.md` command definitions (commit, pr, cw-logs, check-stacks, flag-toggle, test-feature, gh-logs, slack) | Cursor only (submodule) |
| **hooks.json** | Cursor hooks config (e.g. stop-hook for QA). See [Cursor Hooks](https://cursor.com/docs/hooks). | Cursor only (submodule) |

When the repo is used as a submodule at `.cursor`, Cursor loads `.cursor/skills/`, `.cursor/rules/`, `.cursor/agents/`, `.cursor/commands/`, and `.cursor/hooks.json` from the project root.

### Skills (installable via CLI or submodule)

| Skill | Description |
|-------|-------------|
| **cli-qa-release** | Full CLI QA and release cycle: publish RCs, verify in consumer, then revert pins, bump version, commit, push, tag; GitHub Actions publishes to npm. Use when changing or validating CLI functionality or when releasing the CLI. |
| **devops-secrets-auth** | Authenticate with GitHub, npm, Figma, and other tools using the AWS Secrets Manager `devops` secret. Use when you need tokens for `gh`, npm publish, Figma API, or other devops-backed services. |
| **update-cursor-config** | Update Cursor configuration (rules, agents, hooks, skills, commands). Use when changing cursor-config content, adding hooks, or propagating config to consuming repos. Requires consuming repos to update their cursor-config submodule. |

## Branch model

- **main** — Generic rules, agents, commands, and skills. One source of truth.
- **Project branches** (e.g. `marketing-workflow-tools`, `dante-platform`) — Branch from main; add only:
  - `rules/project-overview.mdc`
  - `AGENTS.md`
  - Optional project-only commands

## Maintenance

- **Change a skill:** Edit under `skills/<name>/SKILL.md`. For submodule users: commit and push cursor-config, then in each consuming repo run `git submodule update --remote`. For CLI users: they can run `npx skills update` to pull latest.
- **Change a rule/agent/command:** Edit on `main`. Merge `main` into project branches; in consuming repos run `git submodule update --remote`.
- **Change project-only content:** Edit on that project’s branch only.
