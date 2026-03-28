---
name: release-tag
description: Move or recreate a version tag on a commit and push it to trigger tag-based CI (e.g. npm publish, release artifacts)
---

# /release-tag

Use when you need to **(re)run a release pipeline** that triggers on Git tags (common for npm packages, artifacts, or GitHub Releases).

## Generic workflow

1. **From the repository root** (the repo that owns the tag and the workflow — often the package or monorepo root):
   - Checkout the branch you intend to release (commonly `main`): `git checkout main && git pull`
   - **Inspect** `.github/workflows/` for the workflow that runs on tags (e.g. `on: push: tags: ['v*']` or similar). Adjust tag format (`v1.2.3` vs `1.2.3`) to match what your workflow expects.

2. **Choose or move the tag** (example version `v0.2.2`):
   - To **recreate** an existing tag at the current commit:
     - `git tag -d v0.2.2`
     - `git push origin :refs/tags/v0.2.2`
   - Create an **annotated** tag: `git tag -a v0.2.2 -m "Release 0.2.2"`
   - Push: `git push origin v0.2.2`

3. **Monitor** the workflow (GitHub Actions UI or `gh run list --limit 5`).

## Example (npm-style `v*` tags)

```bash
git checkout main && git pull origin main
git tag -d v0.2.2
git push origin :refs/tags/v0.2.2
git tag -a v0.2.2 -m "Release 0.2.2"
git push origin v0.2.2
```

If your project does **not** use npm or `v*` tags, follow the tag pattern and workflow defined in that repo’s `.github/workflows` instead.
