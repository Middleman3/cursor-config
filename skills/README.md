# Skills

All entries follow the [Agent Skills standard](https://agentskills.io): each folder has a `SKILL.md` with YAML `name` and `description`. Install the whole repo with the [skills CLI](https://github.com/vercel-labs/skills): `npx skills add <org>/cursor-config`.

Optional subdirectories per skill: `scripts/`, `references/`, `assets/`.

## Index

| Skill | Description |
|-------|-------------|
| **add-feature-flag** | Create a feature flag, wire it into the handler as a precondition, and add flag-off test scenarios. Use when adding a new feature that needs a feature flag, or when gating existing functionality behind a flag. |
| **add-lambda** | Scaffold a new Lambda service from scratch — handler, CDK stack, secrets wiring, feature flag, test conditioner, and feature file. Use when the user asks to add a new Lambda, create a new service, or build a new microservice. |
| **backend-conventions** | Backend service conventions — dependencies, composition root, feature flags, Lambda-style builds |
| **cdk-conventions** | CDK infrastructure conventions and patterns |
| **cdk-reviewer** | Reviews CDK synth output for unexpected changes. Use after running the project's synth command. |
| **check-stacks** | List active CloudFormation stacks and check health |
| **cli-qa-release** | Full CLI QA and release cycle — publish RCs, verify in consumer, then revert pins, bump version, commit, push, tag; GitHub Actions publishes to npm. Use when changing or validating CLI functionality or when releasing the CLI. |
| **commit** | Lint, build, and commit with conventional commit format |
| **cw-logs** | Fetch and summarize CloudWatch logs for Lambda executions |
| **flag-toggle** | Toggle a LaunchDarkly feature flag on or off |
| **gh-logs** | Fetch and summarize GitHub Actions build logs |
| **git-workflow** | Branch policy, conventional commits, PR workflow, CI monitoring, versioning |
| **github-pr-tour** | Add concise, lettered PR tour comments via gh (inline on the diff) so reviewers get a fast high-level map—use when opening or polishing a PR for review. |
| **handle-event** | Add or update an event handler pipeline — EventBridge rule, SQS queue (+DLQ), single-purpose Lambda via shared CDK event-handler construct, then run QA for full testing and release. |
| **implement-endpoint** | Implement a new backend endpoint — branching, coding, feature flags, CDK, and test creation. Hands off to the quality-assurance subagent for the release cycle. Use when the user asks to add or build a new endpoint, API route, or feature. |
| **manage-secrets** | Create and manage AWS Secrets Manager secrets via CLI, then update CDK stacks to grant Lambda access. Use when adding API keys, tokens, or credentials for a service. |
| **modify-infra** | Make and verify CDK infrastructure changes — edit stacks, synth, review templates, and deploy. Use when the user asks to change infrastructure, add resources, modify permissions, or update CDK stacks. |
| **observability** | Create and verify observability changes — structured logging, CloudWatch Logs Insights queries, and the observability CDK stack. Use when adding logging to new services, updating log queries, or verifying log output. |
| **pr** | Push branch, create PR, and monitor CI |
| **pr-judgement** | Assess a GitHub PR for direction, risk, scope, and merge readiness (stale branch, security, tests, title/body alignment). Use when judging a PR or deciding merge risk. |
| **prototyping** | Build frontend UIs with Cradle (service worker + IndexedDB) virtualizing the BFF; wire apps via file package.json deps, VITE_CRADLE flag, and Playwright E2E with E2E_CRADLE for virtual vs real API. Use when vibecoding screens, app prototypes, or BFF spec export from traces. |
| **quality-assurance** | Full release lifecycle — verify tests, deploy to staging, run feature tests, push, monitor CI, feature flags decouple deployment from release, merge, tag, GitHub Release, changelog, and optional deployment notification. Use after implementation is complete, or when asked to release, deploy, or merge code. |
| **refactor** | Refactor existing code — branching, restructuring, verifying existing tests pass, updating tests if behavior changes. Hands off to the quality-assurance subagent for the release cycle. Use when the user asks to refactor, restructure, or clean up code. |
| **release-tag** | Recreate a release tag on the current commit and push to trigger the publish workflow |
| **slack** | Send a message to Slack via Incoming Webhook (when the project has a notify command) |
| **test-feature** | Deploy to staging and run feature tests |
| **test-harness** | Test harness architecture and conventions — Godog/Cucumber BDD, Lambda and API paradigms, feature files, tags, TestEnvironment, resource names, extending components |
| **test-specialist** | Expert in the Godog/Cucumber test harness. Creates and debugs feature tests, data conditioners, and API/Lambda step definitions with comprehensive scenario coverage. Use when writing or debugging tests, creating data conditioners, or designing feature test scenarios. |
| **update-skills** | Update a shared Agent Skills repo and Cursor hooks; propagate submodule updates to consuming apps. Use when editing team skills or hooks. |
| **verify-first** | Verify before suggesting, use mise for tools, build after changes |
