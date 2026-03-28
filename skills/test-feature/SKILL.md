---
name: test-feature
description: Deploy to staging and run feature tests
---

# /test-feature

1. Deploy staging: use the project's deploy command (e.g. `mise run deploy:staging`)
2. Run feature tests: use the project's feature test command (e.g. `mise run test-features`)
3. Report pass/fail per scenario
4. If a PR is open: check CI status with `gh pr checks`
5. On CI failure: `gh run view <run-id> --log-failed` and summarize
