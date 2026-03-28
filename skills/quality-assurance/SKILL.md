---
name: quality-assurance
description: Full release lifecycle — verify tests, deploy to staging, run automated tests, push, monitor CI, feature flags, merge, tag, GitHub Release, changelog, and optional notification. Use after implementation or when asked to release, deploy, or merge.
---

# Quality Assurance

You are the release gatekeeper. You receive handoff from implementation work (new endpoints, refactors, new services) and drive the full release cycle. You can also be invoked standalone.

**Conventions below use example commands** (`mise run …`, `tests/features/`, Godog-style layout). **Replace** with this repository’s real deploy, test, and flag commands, and its directory layout.

**Autonomy:** Drive the release all the way to `main` yourself. Never tell the user to "re-run checks," "push an empty commit," or do any manual step to get CI green or to merge. If CI fails because of a dependency (e.g. another repo): fix or push the dependency to its default branch first, then re-trigger this project’s CI (e.g. `git commit --allow-empty -m "chore: re-run CI" && git push`), wait for green, then merge. You own the full path to merged code on `main`.

## Phase 1: Verify test quality

- Confirm automated tests exist for the changed functionality (e.g. feature files under `tests/features/` if the project uses Gherkin/BDD).
- Verify scenarios follow project conventions: customer perspective, happy vs negative tags, no redundant "happy" step tags unless the project requires them.
- Verify test fixtures/wiring exist and are registered (e.g. suite bootstrap in `tests/suite.go` if applicable).
- If the project uses feature flags: verify flag-on and flag-off coverage for gated behavior.
- Verify externally reachable surfaces are covered (HTTP APIs, webhooks, etc., as applicable).
- For event-driven pipelines (e.g. queue → Lambda), verify ingress and negative paths if the project uses them.
- If anything is missing or non-conformant, create or fix before proceeding.

## Phase 2: Deploy to staging

- Run the project’s staging deploy (example: `mise run deploy:staging`).
- If deployment fails: read output, diagnose, fix, retry.

## Phase 3: Run tests against staging

- Run the project’s integration or feature test command (example: `mise run test-features`).
- On failure: inspect output, fix, redeploy if needed, repeat until green.

## Phase 4: Commit and push to feature branch

- Run lint/format and build per project standards.
- Stage, commit with conventional commits (lowercase), `git push -u origin HEAD`.
- Open PR if needed (`gh pr create`), using the project’s PR template if present.

## Phase 5: Monitor branch CI

- Watch checks (e.g. `gh pr checks <pr-number> --watch`).
- On failure: `gh run view <run-id> --log-failed`, fix root cause (including upstream repos if needed), re-trigger CI; do not hand work back to the user for routine green builds.

## Phase 6: Feature flags (when the project uses them)

- Verify the flag exists and defaults are safe for production (example CLI: `ldcli flags get --project <project> --flag <key>` — use your project’s tool and project key).
- Record flag key and dashboard URL for release notes.

## Phase 7: Merge, tag, release, monitor

- Merge when green (e.g. `gh pr merge --squash --delete-branch`).
- Watch default-branch pipeline (e.g. `gh run list --branch main --limit 1` then `gh run view <id> --watch`).
- On live deploy failure: hotfix via same QA pattern.
- **Version and tag** per semantic versioning and project rules.
- **GitHub Release**: `gh release create …` with notes from the PR and flags if applicable.
- **CHANGELOG** if the project keeps one.

## Phase 8: Notification (optional)

- If the project has Slack or other notify commands (example: `mise run notify:slack "<message>"`), send a message with version, what shipped, flag state, links to release and PR.
