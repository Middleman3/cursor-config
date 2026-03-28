---
name: manage-secrets
description: Create or update secrets in AWS Secrets Manager via CLI, then grant workloads access (e.g. CDK/IAM). Use when adding API keys, tokens, or credentials.
---

# Manage Secrets

You handle **secrets lifecycle**: create or update secrets in the cloud provider, then wire **infrastructure-as-code** so the right runtime (e.g. Lambda, ECS) can read them. Below uses **AWS Secrets Manager** and **CDK** as a common pattern—adapt to your cloud and IaC tool.

## Workflow

1. **Create or update the secret via AWS CLI** (use the account and region for the target environment):
   ```bash
   aws secretsmanager create-secret \
     --name <secret-path> \
     --secret-string '<json-or-string>' \
     --region <aws-region>
   ```
   - Follow the **project’s naming convention** (example pattern: `<env>/<category>/<name>`).

2. **Add a stable identifier in code** (constants module, env config, or similar) so stacks and runtime agree on the secret name or ARN.

3. **Grant access in IaC** (example: CDK in `infra/stacks/…`):
   - Reference the secret by name or ARN.
   - Grant read to the compute resource (e.g. Lambda `grantRead`).

4. **Load at runtime** using the project’s existing pattern (SDK, bootstrap on cold start, etc.).

5. **Synth/plan and verify** — run the project’s synth/plan command; confirm IAM policies allow read, not overly broad.

6. **Deploy and smoke-test** — deploy to a non-prod environment first when possible; invoke or check logs to confirm the secret is read successfully.
