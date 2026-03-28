---
name: cdk-conventions
description: CDK infrastructure conventions and patterns
---

# CDK Conventions

## Stack Pattern

- Each leaf stack exposes `AddToStack(scope, props)`.
- `Props` struct embeds `awscdk.StackProps` + `Environment *string`.
- Function names use `Fn_Sub` with `${Env}` token.
- Lambda builds via the project's construct helper (e.g. `constructs.BuildGoLambda(projectRoot, cmdPath)`).

## Workflow

- After changes: wipe `infra/cdk.out`, resynth with the project's synth command (e.g. `mise run synth`), review templates before deploying.
- Review `infra/cdk.out/*.template.json` and `*.nested.template.json` for resource names, logical IDs, parameters.

## Secrets

- Reference existing secrets with `awssecretsmanager.Secret_FromSecretNameV2`.
- Grant Lambda access with `secret.GrantRead(fn, nil)`.
- When the project uses feature flags, set the feature-flag SDK key as an environment variable on Lambdas that need it.
