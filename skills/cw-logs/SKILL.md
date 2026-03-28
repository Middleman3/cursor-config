---
name: cw-logs
description: Fetch and summarize CloudWatch logs for Lambda executions
---

# /cw-logs

1. Determine the Lambda function name from the user's request or infer from the current branch/service (e.g. `my-service-staging`)
2. Find the log group: `/aws/lambda/<function-name>`
3. Fetch recent log events:
   ```bash
   aws logs filter-log-events \
     --log-group-name /aws/lambda/<function-name> \
     --start-time <epoch-ms-15-min-ago> \
     --region us-east-1 \
     --limit 100
   ```
4. If the user provides a request ID or correlation term, filter with `--filter-pattern "<term>"`
5. Parse and summarize: extract ERROR/WARN lines, cold start durations, timeouts, and stack traces
6. If the log group has multiple streams, identify the relevant stream by timestamp or request ID
7. Report: invocation count, errors found, cold starts, and full error context for any failures
