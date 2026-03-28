---
name: update-skills
description: Update a shared Agent Skills repository and Cursor hooks, then propagate changes to apps that consume them (e.g. git submodule). Use when editing team skills, hooks, or syncing consuming repos.
---

# Update shared skills and hooks

Use this skill when you maintain a **shared skills repository** (often named something like `cursor-config` or `team-skills`) whose contents are reused across multiple application repos. Typical layout: **`skills/*/SKILL.md`** (Agent Skills) plus **`hooks.json`** at the repo root for [Cursor Hooks](https://cursor.com/docs/hooks).

## Documentation

- **Cursor Hooks:** [https://cursor.com/docs/hooks](https://cursor.com/docs/hooks)
- **Installing skills:** [skills CLI](https://github.com/vercel-labs/skills) — `npx skills add <org>/<repo>`
- **Agent Skills format:** each skill is a directory with `SKILL.md` and YAML frontmatter (`name`, `description`)

## Example layout (reference repo)

One real-world pattern is a GitHub repo (example: **`your-org/cursor-config`**) containing:

- `skills/<name>/SKILL.md` — team-wide instructions
- `hooks.json` — optional project hooks (e.g. stop-hook for QA loops)

Consuming apps often add that repo as a **git submodule** at **`.cursor`** (or `.cursor-shared`) so Cursor sees `.cursor/skills/` and `.cursor/hooks.json` from the application root.

Adapt names (`your-org/cursor-config`, branch names) to your organization.

## Workflow: edit shared repo and propagate

1. **Edit the shared repository**
   - Add or change `skills/<skill-name>/SKILL.md` and/or `hooks.json`.
   - Commit and push to the appropriate branch (often `main` for shared content; optional long-lived branches for program-specific overlays).

2. **Update each consuming application (when using submodules)**
   - From the app repo root: `git submodule update --init --remote .cursor` (or the path you used for the submodule).
   - Confirm the submodule points at the new commit (`git status`, `git diff .cursor`).
   - Commit the updated submodule reference, e.g. `git add .cursor && git commit -m "chore: update shared cursor skills"` then push.

3. **Verify**
   - Open the app in Cursor and confirm skills and hooks load. Check **Settings → Hooks** if you changed `hooks.json`.

## Hooks-specific notes

- Paths in `hooks.json` are relative to the **application project root** (e.g. `.cursor/hooks/script.sh`).
- After changing hooks in the shared repo, every consuming app must pull the new submodule commit (and commit the new ref if tracked) so `.cursor/hooks.json` updates.

## When to use this skill

- Adding or editing shared skills or hooks.
- Propagating submodule updates to application repos.
- User asks to sync shared config, update the `.cursor` submodule, or roll out hook changes.
