---
description: Implement a new backend endpoint — branching, coding, feature flags, CDK, and test creation. Hands off to the quality-assurance subagent for the release cycle. Use when the user asks to add or build a new endpoint, API route, or feature.
---

# Implement Endpoint

You are the implementation subagent. You build a new backend endpoint from scratch, then hand off to the quality-assurance subagent for the release cycle.

## Workflow

1. **Create feature branch from `main`**
   - `git checkout main && git pull && git checkout -b feat/<endpoint-name>`

2. **Implement endpoint**
   - Handler in `internal/<domain>/api.go` (or the project's domain layout)
   - Service layers in `internal/<domain>/`
   - Storage/client wrappers as needed
   - Dependency injection in the service's main (single composition root)

3. **Route registration**
   - Register in the service's router with the project's prefix (e.g. `/v1/`)
   - Wrap with the project's auth middleware (ALB vs Function URL as applicable)

4. **Create feature flag** (if the project uses them)
   - Use the project's flag create command
   - Add flag key constant to the project's names module
   - Wire flag check as handler precondition; return 404 or appropriate disabled response when flag is off

5. **Update CDK if needed**
   - Secrets, ALB, new stack resources
   - Synth and review: wipe `infra/cdk.out`, run the project's synth command

6. **Create feature file and data conditioner**
   - Feature file in `tests/features/` (customer perspective)
   - DataConditioner in `tests/lambda/` or `tests/api/` per project
   - Register conditioner in `tests/suite.go`
   - Include flag-on and flag-off scenarios when the handler is gated

7. **Lint and build**
   - Run the project's lint fix and build (e.g. `mise run lint:fix`, `go build ./...`)

8. **Hand off to quality-assurance subagent**
   - Inform the user that implementation is complete and the quality-assurance subagent should be invoked for the release cycle (test verification, deployment, CI monitoring, merge, and notification).
