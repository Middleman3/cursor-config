---
name: test-feature
description: Deploy to a non-production environment and run the project’s automated feature or integration tests
---

# /test-feature

1. **Deploy** to the environment tests target (often staging): use **this repo’s** command (example: `mise run deploy:staging`).
2. **Run tests**: use the project’s test entrypoint (example: `mise run test-features` for Gherkin/Godog suites; substitute `npm test`, `pytest`, etc. as appropriate).
3. **Report** pass/fail per scenario or job.
4. If a PR is open: check CI with `gh pr checks`.
5. On CI failure: `gh run view <run-id> --log-failed` and summarize.
