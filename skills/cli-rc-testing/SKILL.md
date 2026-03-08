---
name: cli-rc-testing
description: Publishes npm CLI packages as release candidates (rc) for local manual testing and guides installing and running them in a consumer repo. Use when the user wants to test CLI changes locally by deploying an RC, pulling it from npm, or setting up a consumer to use an RC version.
---

# CLI Release Candidate Testing

Workflow for testing CLI changes manually: publish an RC to npm under a dist-tag, then install and run it in another repo.

## When to use

- User asks to publish a CLI as a release candidate, test an RC locally, or deploy/pull an RC for manual testing.
- User wants to consume a specific RC version (e.g. `0.2.2-rc.0`) in a project that uses the CLI (e.g. via mise).

## 1. Publish the RC (CLI source repo)

1. **Version**: Set the CLI's `package.json` `version` to a prerelease, e.g. patch RC: `0.2.2-rc.0`.
2. **Build**: Build any workspace dependencies first (e.g. `@scope/core`), then the CLI. If `tsc` reports "up to date" but `dist/` is missing, remove `tsconfig.tsbuildinfo` in the dependency package and run `tsc -b` again.
3. **Publish** (auth via one of):
   - **AWS Secrets Manager**: If the account has a secret (e.g. `devops`) with JSON `{"NPM_TOKEN":"..."}`, write a temporary `.npmrc` and publish with it (then delete the file):
     ```bash
     aws secretsmanager get-secret-value --secret-id devops --query SecretString --output text | node -e "
     const s=JSON.parse(require('fs').readFileSync(0,'utf8'));
     require('fs').writeFileSync('.npmrc.publish','//registry.npmjs.org/:_authToken='+s.NPM_TOKEN+'\n');
     "
     npm publish --workspace=@scope/cli --tag rc --access public --userconfig .npmrc.publish
     rm -f .npmrc.publish
     ```
     (Use `jq -r .NPM_TOKEN` instead of the node one-liner if jq is available.)
   - **Local**: Run `npm login` first, then the same `npm publish` command (no `--userconfig`).
4. Result: the version is on the `rc` dist-tag; `latest` is unchanged.

## 2. Consume the RC (consumer repo)

1. **Pin the RC** in the consumer's config (e.g. `.mise.toml`):
   ```toml
   "npm:@scope/cli" = "0.2.2-rc.0"
   ```
   Or use `"rc"` to track the latest RC.
2. **Install and run**:
   ```bash
   mise install
   mise run <task>   # or: mise exec -- <cli> <args>
   ```

## Conventions

- Use exact RC version (e.g. `0.2.2-rc.0`) in the consumer for reproducible manual testing.
- Do not commit npm auth tokens; use AWS Secrets Manager (e.g. secret `devops` with `NPM_TOKEN`), `npm login`, or CI secrets for publish.
- Align with the project rule `.cursor/rules/cli-rc-testing.mdc` when present.
