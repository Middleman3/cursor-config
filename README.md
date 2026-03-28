# cursor-config

Shared **Agent Skills** and Cursor **hooks** for backend/CDK/Lambda-style projects. Layout matches the [open agent skills](https://agentskills.io) ecosystem and the **[skills CLI](https://github.com/vercel-labs/skills)** (`npx skills add`).

## Layout

| Path | Purpose |
|------|---------|
| **`skills/<name>/SKILL.md`** | All agent instructions: conventions (formerly project rules), workflows (formerly agents), and slash-command playbooks (formerly commands). Each file has YAML frontmatter with `name` and `description`. |
| **`hooks.json`** | Cursor hooks (e.g. stop-hook for QA). Loaded when this repo is the `.cursor` submodule. |

There are no separate `rules/`, `agents/`, or `commands/` directories—everything installable and discoverable lives under **`skills/`**, per the [skills CLI discovery paths](https://github.com/vercel-labs/skills).

## Install skills (skills CLI)

```bash
# From GitHub (replace <org> with your org)
npx skills add <org>/cursor-config

# List skills without installing
npx skills add <org>/cursor-config --list

# Install specific skills
npx skills add <org>/cursor-config --skill cli-qa-release --skill git-workflow

# From a local clone
npx skills add ./path/to/cursor-config
```

The CLI installs into the target agent’s skill directory (for Cursor: project `.agents/skills/` or global `~/.cursor/skills/` with `-g`). See the [skills CLI README](https://github.com/vercel-labs/skills) for `--agent`, `--skill`, and `--copy` options.

## Full Cursor setup (git submodule)

Use this repo as a **git submodule** at **`.cursor`** so the project gets **`skills/`** and **`hooks.json`** in one place.

```bash
git submodule add -b <branch> https://github.com/<org>/cursor-config.git .cursor
```

After clone: `git submodule update --init --remote`

Cursor loads agent skills from `.cursor/skills/` and hooks from `.cursor/hooks.json` when the submodule is present.

### Submodule vs always-on rules

Previously, `.mdc` files under `rules/` were **project rules** (often always applied). Those instructions now live as **skills** (on-demand). Repos that relied on automatic rule application can add local **Project Rules** or **`AGENTS.md`** that point at the most important skill names, or pin a small set of rules in the consuming repo only.

## Index

See [skills/README.md](skills/README.md) for a table of all skills.

## Branch model

- **main** — Generic skills and hooks. One source of truth.
- **Project branches** (e.g. `marketing-workflow-tools`, `dante-platform`) — Branch from `main`; add only project-specific skills (e.g. `skills/project-overview/SKILL.md`) and optional `AGENTS.md` on that branch.

## Maintenance

- **Change a skill:** Edit `skills/<name>/SKILL.md`. Submodule users: commit and push cursor-config, then in each consuming repo `git submodule update --remote` and commit the new submodule ref. CLI users: `npx skills update`.
- **Change hooks:** Edit `hooks.json`; propagate the same submodule update flow.
