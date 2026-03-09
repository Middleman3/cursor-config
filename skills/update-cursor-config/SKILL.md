---
name: update-cursor-config
description: Update Cursor configuration (rules, agents, hooks, skills, commands). Use when changing cursor-config content, adding hooks, or propagating config to consuming repos. Requires consuming repos to update their cursor-config submodule.
---

# Update Cursor Config

Use this skill when adding or changing Cursor configuration that lives in the **cursor-config** repo (rules, agents, hooks, skills, commands), and when ensuring **consuming repos** receive those updates.

## Documentation

- **Cursor Hooks (config, events, prompt/command hooks):** [https://cursor.com/docs/hooks](https://cursor.com/docs/hooks)
- **cursor-config layout:** See `README.md` in the cursor-config repo (rules/, agents/, skills/, commands/; project-level hooks live in `hooks.json` at repo root when `.cursor` is the submodule).

## Where config lives

- **cursor-config** is the source of truth. It is used as a **git submodule** at `.cursor` (or `.cursor-shared` with a symlink) in each consuming repo.
- **Project-level hooks** are defined in `cursor-config/hooks.json`. When a repo has `.cursor` pointing at cursor-config, that file is at `<repo>/.cursor/hooks.json` and Cursor loads it from the project root.
- **Consuming repos** (e.g. dante-platform, waypoint-framework and their sub-repos like platform, waypoint, marketing-workflow-tools, cloudflare-ld-worker) must **update the submodule** to get new rules, agents, hooks, or skills.

## Workflow: change cursor-config and propagate

1. **Edit in cursor-config**
   - Change or add files under cursor-config (e.g. `rules/*.mdc`, `agents/*.md`, `skills/*/SKILL.md`, `hooks.json`, `commands/*.md`).
   - Commit and push cursor-config to the appropriate branch (usually `main` for generic content; project branches for project-specific content).

2. **Update consuming repos (required)**
   - In **each** repo that uses cursor-config as a submodule (at `.cursor` or `.cursor-shared`):
     - From the repo root: `git submodule update --init --remote .cursor` (or ` .cursor-shared` if that is the submodule path).
     - Confirm the submodule ref moved to the new commit (e.g. `git status`, `git diff .cursor`).
     - Commit the updated submodule reference: e.g. `git add .cursor && git commit -m "chore: update cursor-config submodule"` then push.
   - If the workspace is a monorepo or multi-repo layout, run the submodule update (and optional commit/push) in **each** consuming repo root that has the cursor-config submodule.

3. **Verify**
   - Open a consuming repo in Cursor and confirm the new config is active (e.g. new rule or hook behavior). For hooks, Cursor Settings → Hooks tab can help confirm loading.

## Hooks-specific notes

- Hooks config: `cursor-config/hooks.json`. Schema and events: [Cursor Hooks docs](https://cursor.com/docs/hooks).
- Project hooks run from the **project root**; paths in `hooks.json` are relative to project root (e.g. `.cursor/hooks/script.sh` for a script next to the config).
- After changing `hooks.json` in cursor-config, consuming repos must run `git submodule update --remote` (and commit the new submodule ref if they track it) so their `.cursor/hooks.json` is updated.

## When to use this skill

- Adding or editing Cursor rules, agents, skills, or commands in cursor-config.
- Adding or changing hooks (e.g. `hooks.json`, hook scripts).
- Propagating cursor-config changes to dante-platform, waypoint-framework, or other repos that use cursor-config as a submodule.
- User asks to "update cursor config", "sync cursor-config", "update the .cursor submodule", or "add a hook" in the shared config.
