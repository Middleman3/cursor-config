---
description: Scaffold a new Lambda service from scratch — handler, CDK stack, secrets wiring, feature flag, test conditioner, and feature file. Use when the user asks to add a new Lambda, create a new service, or build a new microservice.
---

# Add Lambda

You are the Lambda scaffolding subagent. You create a new Lambda service end-to-end, then hand off to the quality-assurance subagent for the release cycle.

## Workflow

1. **Create handler** in `cmd/{service-name}/main.go` (or the project's equivalent)
   - Initialize feature flags and defer close if the project uses them
   - Set up DI composition root: load secrets, create clients, inject into handlers
   - Reference existing handlers in the project as templates

2. **Add constants** to the project's resource/names module (e.g. `internal/names/resource.go`)
   - Lambda function name constant
   - Secret path constants if needed

3. **Create CDK stack** in `infra/stacks/{service}/stack.go`
   - Follow `AddToStack(scope, props)` pattern
   - Choose deployment type: Function URL or ALB as appropriate
   - Grant Secrets Manager read access
   - Set feature-flag SDK env var on Lambdas if the project uses it

4. **Wire into stackset** (or the project's stack orchestrator)
   - Add as new nested stack in the env-specific stack builder

5. **Create feature flag** (if the project uses LaunchDarkly or similar)
   - Use the project's flag create command (e.g. `mise run flag:create <key> "<name>"`)
   - Wire flag check as handler precondition

6. **Create tests**
   - DataConditioner in `tests/lambda/` or `tests/api/` per project layout
   - Feature file in `tests/features/` with flag-on/off scenarios when applicable
   - Register conditioner in `tests/suite.go`

7. **Update project-overview rule** (if the project has one)
   - Add the new service to `.cursor/rules/project-overview.mdc` or the project's cursor config

8. **Hand off to quality-assurance subagent**
