---
name: release-tag
description: Recreate a release tag on the current commit and push to trigger the publish workflow
---

# /release-tag

Use when you need to (re)run the npm publish workflow (e.g. after fixing the pipeline or moving the tag to a newer commit).

1. **From the repo root** (e.g. the monorepo that has `.github/workflows/publish.yml`):
   - Ensure you're on the branch you want to tag (e.g. `main`): `git checkout main && git pull origin main`
   - Choose the tag (e.g. `v0.2.2`). If recreating an existing tag:
     - Delete the tag locally: `git tag -d <tag>`
     - Delete the tag on the remote: `git push origin :refs/tags/<tag>`
   - Create an annotated tag on the current commit: `git tag -a <tag> -m "Release <version>"`
   - Push the tag: `git push origin <tag>`
2. The push triggers the workflow that runs on `push: tags: v*`. Monitor in the Actions tab or with `gh run list --limit 3`.

Example for `v0.2.2`:

```bash
git checkout main && git pull origin main
git tag -d v0.2.2
git push origin :refs/tags/v0.2.2
git tag -a v0.2.2 -m "Release 0.2.2"
git push origin v0.2.2
```
