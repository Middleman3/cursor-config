---
name: github-actions-oidc-aws
description: GitHub Actions OIDC to AWS — ECR login, build/push image, ECS task definition render/deploy, CodeDeploy canary fields. Use when editing deploy workflows or IAM trust.
---

# GitHub Actions + AWS (OIDC)

## Permissions

```yaml
permissions:
  contents: read
  id-token: write
```

## Assume role

- **aws-actions/configure-aws-credentials@v4** with `role-to-assume: ${{ secrets.AWS_DEPLOY_ROLE_ARN }}`.
- IAM trust policy: `token.actions.githubusercontent.com`, sub claim scoped to repo/ref as needed.

## ECR

- **aws-actions/amazon-ecr-login@v2** then `docker build` / `docker push` with `$REGISTRY/$REPO:$TAG`.

## ECS deploy

- **aws-actions/amazon-ecs-render-task-definition@v1** — update `image` for the service container name.
- **aws-actions/amazon-ecs-deploy-task-definition@v2** — direct deploy **or** CodeDeploy:
  - `codedeploy-appspec`, `codedeploy-application`, `codedeploy-deployment-group` for blue/green canary.

## Launch mode

- **pre-launch:** direct ECS deploy (no CodeDeploy args).
- **live:** pass CodeDeploy parameters for canary; tighten Trivy image `exit-code` via `DANTE_LAUNCH_MODE`.

## SARIF

- Trivy image scan: upload SARIF with `github/codeql-action/upload-sarif` (`if: always()` when scan may fail).
