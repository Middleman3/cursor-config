---
description: Fetch and summarize GitHub Actions build logs
---

# /gh-logs

1. If a PR number is provided, get the latest run: `gh pr checks <number>` to list check runs
2. If no PR, get the latest run on current branch: `gh run list --branch $(git branch --show-current) --limit 5`
3. Let the user pick a run (or use the latest failed one)
4. Fetch the full failed job logs: `gh run view <run-id> --log-failed`
5. If output is too large, fetch job-specific logs: `gh run view <run-id> --job <job-id> --log`
6. Summarize the failure: which job failed, which step, the error message, and a suggested fix
7. If no failures, report all checks passing with their durations
