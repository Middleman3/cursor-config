---
description: Create and verify observability changes — structured logging, CloudWatch Logs Insights queries, and the observability CDK stack. Use when adding logging to new services, updating log queries, or verifying log output.
---

# Observability

You are the observability subagent. You create or update structured logging and CloudWatch Logs Insights queries, then verify the full pipeline from Lambda invocation through to query results.

## Architecture

Structured logs flow through:
`Lambda handler → slog (JSON to stdout) → CloudWatch Logs → Logs Insights saved queries`

The logging layer has two categories of slog attributes:
- **AOP (aspect-oriented)**: Injected by middleware/context automatically (`request_id`, `lambda_request_id`, `endpoint`, `trace_id`, `span_id`). These appear as top-level JSON fields and as Logs Insights display columns.
- **Handler-added**: Set in handler code (e.g. `handler`, `event`, `repoPath`). These appear as JSON fields AND get inlined into the `msg` field as `msg (key: val, key: val)` for human readability — only string values within a strict character limit qualify for inlining.

The observability CDK stack (`infra/stacks/observability/`) is environment-agnostic: it defines Logs Insights saved queries that reference per-environment log groups.

## Adding Logging to a New Lambda

1. **Service Lambdas (HTTP via API Gateway)**
   - In the Lambda handler wrapper: inject request ID and traceparent via `logger.WithLambdaRequest(ctx, apiGwRequestID, headers)` and `logger.WithLambdaRequestID(ctx, lambdaRequestID)`
   - Wrap the HTTP handler with `logger.ContextMiddleware(mux)` BEFORE auth middleware — this adds `endpoint` (with HTTP method, e.g. `POST /inference`) and extracts `traceparent`
   - In handlers: use `logger.FromContext(r.Context()).Info("msg", "key", "val")`

2. **Event handler Lambdas (SQS consumers)**
   - Extract `requestID` from `lambdacontext.FromContext(ctx)`
   - Inject via `logger.WithRequestID(ctx, requestID)`
   - Use `logger.FromContext(ctx).Info("msg", "key", "val")`

3. **Register the log group** in the observability stack
   - Add `/aws/lambda/<function-name-staging>` to the staging log groups slice
   - Add `/aws/lambda/<function-name-live>` to the live log groups slice

## Adding or Updating Logs Insights Queries

- Queries live in `infra/stacks/observability/stack.go` as Go string constants
- Use CloudWatch's automatic JSON field discovery — do NOT add `parse` commands for JSON-formatted logs
- Filter Lambda runtime noise (INIT_START, START, END, REPORT) with `filter ispresent(request_id)` rather than pattern-matching on message text
- Include commented-out filter templates (`#| filter field = "value"`) ordered broadest-to-most-specific: `level`, `endpoint`, `handler`, `request_id`, `lambda_request_id`, `msg like`
- Sort `@timestamp asc` (oldest first) — use time range and filters to scope, not sort direction
- Display `msg` (the enriched message) rather than `@message` — this gives the human-readable summary with inlined attrs

## Updating the Shared Logger

When modifying `internal/logger/`:
- New AOP keys must be added to the `aopKeys` set in `handler.go` to prevent them from being inlined into `msg`
- New context keys need: context key var, `With*` setter, getter function (in `context.go`), and a corresponding block in `FromContext` (in `logger.go`)
- The `enrichHandler` wraps the JSON handler — record-level attrs (from `.Info("msg", k, v)` calls) that are non-AOP, string-typed, and within `maxInlineValueLen` get appended to `msg`
- AOP attrs are injected via `.With()` in `FromContext` so they are NOT in the record and cannot be accidentally inlined

## Verification (Required)

After any observability change, verify the full pipeline:

### Step 1: Deploy
- Deploy the application stack: the project's staging deploy command (e.g. `mise run deploy:staging`)
- Deploy the observability stack: the project's observability deploy command (e.g. `mise run deploy:observability`)

### Step 2: Invoke Lambdas directly
- Use `aws lambda invoke --function-name <name> --payload '{}' --cli-binary-format raw-in-base64-out /tmp/out.json` for each Lambda
- This validates the logging layer works in isolation

### Step 3: Check raw logs
- Use `aws logs filter-log-events --log-group-name /aws/lambda/<name> --start-time <epoch-ms> --limit 10` to inspect actual JSON output
- Verify: AOP fields present at top level, handler attrs present, `msg` field contains enriched message with inlined attrs, no `!BADKEY` artifacts

### Step 4: Invoke via HTTP API (integration test)
- Hit each endpoint through API Gateway with a `traceparent` header to validate the full middleware chain
- Verify: `endpoint` includes HTTP method, `trace_id` and `span_id` populated from traceparent, `lambda_request_id` present alongside `request_id`

### Step 5: Run the Logs Insights query
- CloudWatch Logs Insights has an ingestion delay (typically 15–60 seconds). Wait, then retry with a wider time window if needed
- Use the project's saved query or run ad-hoc:
  ```bash
  aws logs start-query \
    --log-group-names "/aws/lambda/<fn1>" "/aws/lambda/<fn2>" \
    --start-time <epoch> --end-time <epoch> \
    --query-string '<query>'
  ```
- Wait for results: `aws logs get-query-results --query-id <id>`
- Verify: Lambda runtime noise is filtered out, `msg` shows enriched messages, display columns match the saved query definition

### Common Pitfalls
- `!BADKEY` in logs: passing `[]slog.Attr` as a single arg to `slog.Logger.With()` — must convert to `[]any` and spread
- `MalformedQueryException: Ephemeral field already defined`: do not use `parse` for fields that CloudWatch auto-discovers from JSON
- 0 results from Insights: CloudWatch ingestion lag — wait 15–60s, widen the time window, verify logs exist via `filter-log-events` first
- Missing log groups: Lambda log groups are created on first invocation — invoke each Lambda at least once before querying
