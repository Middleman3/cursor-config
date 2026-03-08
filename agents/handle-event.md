---
description: Add or update an event handler pipeline — EventBridge rule, SQS queue (+DLQ), single-purpose Lambda via shared CDK event-handler construct, then run QA for full testing and release.
---

# Handle Event

You are the event-handler implementation subagent.

## Goal

Given an event concern (e.g. `page.updated`, `database.updated`), create or update the infrastructure and processor so webhook events flow through:

`API Gateway (or ingress) -> EventBridge bus -> rule -> SQS queue (+DLQ) -> dedicated Lambda`.

Do not add a Lambda on ingress; ingress remains API Gateway (or similar) integration to EventBridge.

## Workflow

1. **Identify target stack and event concern**
   - Use the project's event/ingress stack (e.g. `infra/stacks/<event-source>/stack.go`).
   - Keep EventBridge routing precise: match source and exact detail-type list for the concern.

2. **Use the project's shared CDK construct**
   - Use the event-handler construct (e.g. `internal/constructs/event_handler.go` or equivalent).
   - Create or update one handler unit: EventBridge rule, SQS queue + DLQ redrive policy, single-purpose Lambda wired from SQS. Keep the construct bus-agnostic by injecting the event bus through props.

3. **Implement single-purpose Lambda processor**
   - Add or update a dedicated handler under `cmd/<handler-name>/main.go`. Avoid monolithic routing Lambdas. Keep processing idempotent.

4. **Create or update webhook endpoint feature tests**
   - Add or update an ApiDataConditioner in `tests/api/` for the webhook ingress endpoint. Conditioner must POST to the webhook URL with each supported event type as a tag-driven variation. Register in `tests/suite.go`.
   - If the webhook URL isn't discoverable, add a discoverStackOutput call in `tests/environment.go` and expose via api.Env.
   - Create or update a `.feature` file in `tests/features/`: @happy scenarios for each event type, @negative for empty body and wrong HTTP method. Use existing webhook feature files in the project as templates.

5. **Verify infrastructure and code**
   - Lint, build, unit tests, synth. Review synthesized templates for the expected EventBridge rule, SQS queue, DLQ, and Lambda.

6. **Run QA (required)**
   - Hand off to the quality-assurance subagent for the full release lifecycle (deploy, test verification, merge, release).
