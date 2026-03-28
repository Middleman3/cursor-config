---
name: pr
description: Push branch, create PR, and monitor CI
---

# /pr

1. Push branch: `git push -u origin HEAD`
2. Create PR via `gh pr create`:
   - Title: conventional commit format, all lowercase
   - Body: filled per `.github/pull_request_template.md` if present
3. Poll CI: `gh pr checks <number> --watch`
4. Report outcome
5. On failure: `gh run view <run-id> --log-failed` and summarize the error
