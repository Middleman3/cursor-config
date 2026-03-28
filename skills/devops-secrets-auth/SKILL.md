---
name: devops-secrets-auth
description: Authenticate with GitHub, npm, Figma, and other tools using the AWS Secrets Manager devops secret. Use when you need tokens or credentials for gh, npm publish, Figma API, or other devops-backed services.
---

# Authenticate via AWS Secrets Manager (devops)

Use this skill when you need to **authenticate with GitHub** (e.g. `gh` CLI, git over HTTPS), **npm** (publish), **Figma** (REST API), or other tools whose tokens are stored in the **AWS Secrets Manager** secret **`devops`**.

## Prerequisites

- **AWS credentials** configured (e.g. `aws configure`, SSO, or env vars) so `aws secretsmanager get-secret-value --secret-id devops` succeeds.
- Never commit tokens; use env vars or local config files (e.g. `.npmrc.publish`) that are in `.gitignore`.

## Fetching the devops secret

The secret is a **JSON object**. Fetch once and parse to get individual keys.

**Raw JSON (inspect keys):**

```bash
aws secretsmanager get-secret-value --secret-id devops --query SecretString --output text
```

**Extract a single key (Node one-liner):**

```bash
aws secretsmanager get-secret-value --secret-id devops --query SecretString --output text | node -e "const j=JSON.parse(require('fs').readFileSync(0,'utf8')); console.log(j.KEY_NAME||'');"
```

Replace `KEY_NAME` with the key you need (e.g. `NPM_TOKEN`, `FIGMA_TOKEN`, `GH_TOKEN`).

## GitHub

- **Typical key:** `GH_TOKEN` or `GITHUB_TOKEN` (check the secret’s keys).
- **Export for current shell:**

  ```bash
  export GH_TOKEN=$(aws secretsmanager get-secret-value --secret-id devops --query SecretString --output text | node -e "const j=JSON.parse(require('fs').readFileSync(0,'utf8')); console.log(j.GH_TOKEN||j.GITHUB_TOKEN||'');")
  ```

- **Use with `gh`:** After exporting, `gh auth status` should show you as logged in, and `gh pr create`, `gh run view`, etc. will use the token.
- **Use with git:** For HTTPS clone/push, you can use the token as password or configure Git to use it (e.g. `git config --global url."https://${GH_TOKEN}@github.com/".insteadOf "https://github.com/"` when needed).

## npm (publish)

- **Key:** `NPM_TOKEN`.
- **Pattern:** Write a local `.npmrc.publish` (add to `.gitignore`); use it only for publish. See **cursor-config/skills/cli-qa-release/SKILL.md** for the full one-liner that creates `.npmrc.publish` and the `npm publish` command.

**Quick export (for scripts that read env):**

```bash
export NPM_TOKEN=$(aws secretsmanager get-secret-value --secret-id devops --query SecretString --output text | node -e "const j=JSON.parse(require('fs').readFileSync(0,'utf8')); console.log(j.NPM_TOKEN||'');")
```

## Figma (REST API)

- **Key:** `FIGMA_TOKEN`.
- **Export for current shell:**

  ```bash
  export FIGMA_TOKEN=$(aws secretsmanager get-secret-value --secret-id devops --query SecretString --output text | node -e "const j=JSON.parse(require('fs').readFileSync(0,'utf8')); console.log(j.FIGMA_TOKEN||'');")
  ```

- Use when running design scripts or any automation that calls `api.figma.com`. See project-specific Figma skills if the repo has them.

## Other tools

If the `devops` secret has other keys (e.g. Slack, LaunchDarkly, or custom tokens), use the same pattern:

1. Confirm the key name in the secret (inspect raw JSON if needed).
2. Replace `KEY_NAME` in the Node one-liner above.
3. `export VAR_NAME=$(...)` for the current shell, or write to a local config file and add that file to `.gitignore`.

## When to use this skill

- User asks to authenticate with GitHub, npm, Figma, or “devops” tools.
- Scripts or commands need `GH_TOKEN`, `GITHUB_TOKEN`, `NPM_TOKEN`, `FIGMA_TOKEN`, or other keys from the devops secret.
- Documenting or automating auth that uses the shared `devops` secret in AWS Secrets Manager.

## Related

- **cli-qa-release** skill: npm publish flow and `.npmrc.publish` from `NPM_TOKEN` in `devops`.
- **update-cursor-config** skill: no direct auth; cursor-config itself may reference this skill for agent workflows that need tokens.
