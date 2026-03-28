---
name: update-cursor-config
description: Update Cursor configuration (hooks and skills in cursor-config). Use when changing cursor-config content, adding hooks, or propagating config to consuming repos. Requires consuming repos to update their cursor-config submodule.
---

# Update Cursor Config

Use this skill when adding or changing shared Cursor configuration in the **cursor-config** repo—everything lives as **Agent Skills** under `skills/*/SKILL.md` plus **`hooks.json`** at the repo root—and when ensuring **consuming repos** receive those updates.

## Documentation

- **Cursor Hooks (config, events, prompt/command hooks):** [https://cursor.com/docs/hooks](https://cursor.com/docs/hooks)
- **Agent Skills and the [skills CLI](https://github.com/vercel-labs/skills):** Each capability is `skills/<name>/SKILL.md` with YAML `name` and `description`. Install with `npx skills add <org>/cursor-config` (see repo `README.md`).
- **cursor-config layout:** `skills/` (all instructions: former rules, agents, and slash commands), `hooks.json`. No separate `rules/`, `agents/`, or `commands/` folders.

## Where config lives

- **cursor-config** is the source of truth. It is used as a **git submodule** at `.cursor` (or `.cursor-shared` with a symlink) in each consuming repo.
- **Project-level hooks** are defined in `cursor-config/hooks.json`. When a repo has `.cursor` pointing at cursor-config, that file is at `<repo>/.cursor/hooks.json` and Cursor loads it from the project root.
- **Consuming repos** (e.g. dante-platform, waypoint-framework and their sub-repos) must **update the submodule** to get new skills or hooks.

## Workflow: change cursor-config and propagate

1. **Edit in cursor-config**
   - Add or edit `skills/<skill-name>/SKILL.md` and/or `hooks.json`.
   - Commit and push cursor-config to the appropriate branch (usually `main` for generic content; project branches for project-specific content).

2. **Update consuming repos (required)**
   - In **each** repo that uses cursor-config as a submodule (at `.cursor` or `.cursor-shared`):
     - From the repo root: `git submodule update --init --remote .cursor` (or `.cursor-shared` if that is the submodule path).
     - Confirm the submodule ref moved to the new commit (e.g. `git status`, `git diff .cursor`).
     - Commit the updated submodule reference: e.g. `git add .cursor && git commit -m "chore: update cursor-config submodule"` then push.
   - If the workspace is a monorepo or multi-repo layout, run the submodule update (and optional commit/push) in **each** consuming repo root that has the cursor-config submodule.

3. **Verify**
   - Open a consuming repo in Cursor and confirm skills and hooks load. For hooks, Cursor Settings → Hooks tab can help confirm loading.

## Hooks-specific notes

- Hooks config: `cursor-config/hooks.json`. Schema and events: [Cursor Hooks docs](https://cursor.com/docs/hooks).
- Project hooks run from the **project root**; paths in `hooks.json` are relative to project root (e.g. `.cursor/hooks/script.sh` for a script next to the config).
- After changing `hooks.json` in cursor-config, consuming repos must run `git submodule update --remote` (and commit the new submodule ref if they track it) so their `.cursor/hooks.json` is updated.

## When to use this skill

- Adding or editing skills or hooks in cursor-config.
- Propagating cursor-config changes to repos that use cursor-config as a submodule.
- User asks to "update cursor config", "sync cursor-config", "update the .cursor submodule", or "add a hook" in the shared config.
