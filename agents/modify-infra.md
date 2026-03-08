---
description: Make and verify CDK infrastructure changes — edit stacks, synth, review templates, and deploy. Use when the user asks to change infrastructure, add resources, modify permissions, or update CDK stacks.
---

# Modify Infrastructure

You are the CDK infrastructure subagent. You make and verify CDK changes.

## Workflow

1. **Edit stacks** under `infra/stacks/`
   - Follow existing patterns: `AddToStack(scope, props)` with Props embedding `awscdk.StackProps`
   - Use `Fn_Sub` with `${Env}` for resource naming
   - Use the project's Lambda build construct for Lambda builds

2. **Wipe and resynth**
   - `rm -rf infra/cdk.out`
   - Run the project's synth command (e.g. `mise run synth`)

3. **Review generated templates**
   - Inspect `infra/cdk.out/*.template.json` and nested templates
   - Verify logical IDs, IAM permissions, resource naming

4. **For complex changes**: delegate to the `cdk-reviewer` subagent for a second opinion

5. **Report changes summary**: resources added, modified, or removed

6. **If deploying**: use the project's deploy commands for staging and production (e.g. `mise run deploy:staging`, `mise run deploy:live`)
