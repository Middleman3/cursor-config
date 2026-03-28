---
name: observability
description: Add or change structured logging, log queries, and infra for observability (e.g. CloudWatch). Adapt paths and logger APIs to your stack.
---

# Observability

You improve **structured logging**, **log exploration** (e.g. CloudWatch Logs Insights), and any **IaC** that registers log groups or saved queries. The steps below use an **example stack**: AWS Lambda, Go `slog` JSON to stdout, CloudWatch, and CDK—**map** each step to your language, logger, and repo layout.

## Example architecture (reference)

A common pattern:

`Service → structured logs (JSON) → CloudWatch Logs → saved queries / dashboards`

**Illustrative attribute types** (names vary by project):

- **Request context** — request IDs, trace IDs, route identifiers (often from middleware).
- **Business fields** — set in handlers; may be duplicated into a human-readable `msg` line depending on logger design.

**IaC** might live under paths like `infra/stacks/observability/` (example); your repo may use `terraform/`, `cdk/`, etc.

## Adding logging to a new service

1. **HTTP handlers** — Ensure request-scoped IDs and tracing headers are attached **before** business logic; log with the project’s logger API.
2. **Workers / queue consumers** — Propagate invocation/request IDs from the runtime into the logger context.
3. **Register log groups** in observability IaC if the project maintains an explicit list (staging vs production groups, etc.).

Use the **existing** `internal/logger` (or equivalent) package in the repo as the template—do not invent a second logging style.

## Log queries (e.g. CloudWatch Logs Insights)

- Prefer **automatic JSON field discovery** where the platform supports it; avoid redundant `parse` that duplicates fields.
- Filter runtime noise (cold start lines, etc.) with field presence filters when possible.
- Sort by time ascending for incident timelines unless the team standard differs.
- Prefer enriched **message** fields if your logger emits them for human scanability.

## Changing the shared logger package

When editing the project’s logger module (example path `internal/logger/`):

- Keep **context keys**, **setters**, and **enrichment rules** consistent; follow existing patterns for what gets inlined vs structured-only.
- Run unit tests and a quick local smoke log line if the project has them.

## Verification (required after material changes)

1. **Deploy** app and observability stacks per project commands (examples: `mise run deploy:staging`, `mise run deploy:observability`—use what exists).
2. **Invoke** the service (direct invoke, HTTP, or queue) and confirm logs show expected fields.
3. **Raw log tail** — e.g. `aws logs filter-log-events --log-group-name <group> --start-time …` (adjust region and auth).
4. **Integration path** — exercise the real entrypoint (API with trace headers if applicable).
5. **Saved query or ad-hoc Insights query** — allow for CloudWatch ingestion delay (often tens of seconds).

### Common pitfalls (generic)

- Mismatched **context** API usage producing malformed attributes.
- **Duplicate field** errors in query languages when mixing `parse` with auto-detected JSON.
- **Empty query results** due to ingestion lag—widen time range and confirm raw logs first.
- **Missing log groups** until first invocation creates the group.
