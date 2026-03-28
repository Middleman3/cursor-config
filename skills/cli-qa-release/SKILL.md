---
name: cli-qa-release
description: Full CLI QA and release cycle — publish RCs, verify in consumer, then revert pins, bump version, commit, push, tag; GitHub Actions publishes to npm. Use when changing or validating CLI functionality or when releasing the CLI.
---

# CLI QA and Release

Covers the full cycle: (1) publish an RC and verify in a consumer repo, (2) when validated, cut the stable release: revert consumer pins to latest, bump version, commit, push, tag; let GitHub Actions publish to npm; verify the new version on the registry.

## When to use

- Changing or validating CLI functionality (especially when the CLI is consumed by another repo).
- User asks to publish an RC, test in consumer, or release the CLI (stable).

## 1. Publish the RC (CLI source repo)

1. **Version**: Set the CLI's `package.json` `version` to a prerelease, e.g. `0.2.2-rc.0`.
2. **Build**: Build workspace dependencies first (e.g. core), then the CLI. If `tsc` reports "up to date" but `dist/` is missing, remove `tsconfig.tsbuildinfo` in the dependency and run `tsc` again.
3. **Publish** (auth via one of):
   - **AWS Secrets Manager**: Secret `devops` with `NPM_TOKEN`. Write `.npmrc.publish` in the repo root (add to `.gitignore`; leave locally). Then:
     ```bash
     aws secretsmanager get-secret-value --secret-id devops --query SecretString --output text | node -e "
     const s=JSON.parse(require('fs').readFileSync(0,'utf8'));
     require('fs').writeFileSync('.npmrc.publish','//registry.npmjs.org/:_authToken='+s.NPM_TOKEN+'\n');
     "
     npm publish --workspace=@scope/cli --tag rc --access public --userconfig .npmrc.publish
     ```
   - **Local**: `npm login`, then the same `npm publish` (no `--userconfig`).
4. Result: version is on the `rc` dist-tag; `latest` unchanged.

## 2. Verify in consumer

1. In the consumer repo, point the CLI at the RC (e.g. `.mise.toml`: `"npm:@scope/cli" = "0.2.2-rc.0"` or `"rc"`).
2. `mise install` then run the consumer's command (e.g. `mise run validate-all`). Confirm expected output before proceeding to release.

## 3. Stable release (when validated)

1. **Revert pins**: In the consumer repo, set the CLI back to `"latest"` (or `"rc"` if you prefer to track latest RC) in `.mise.toml`. Commit if that repo is tracked.
2. **Bump version**: In the CLI repo, set `package.json` `version` to the stable version (e.g. `0.2.2`; drop the `-rc.N` suffix).
3. **Commit and push**: Commit the version bump (and any uncommitted changes), push to the default branch.
4. **Tag**: Create and push an annotated tag matching the version (e.g. `v0.2.2`). GitHub Actions that run on `push: tags: v*` will publish to npm.
   ```bash
   git tag -a v0.2.2 -m "Release 0.2.2"
   git push origin v0.2.2
   ```
5. **Verify on npm**: After the workflow completes, confirm the new version is on the registry (e.g. `npm view @scope/cli versions` or the package page).

## Conventions

- Use exact RC version in the consumer during QA for reproducibility.
- Keep `.npmrc.publish` local and in `.gitignore`.
- When this repo is used as a submodule at `.cursor`, this skill is the single source for CLI QA/release guidance (replaces the old always-on `.mdc` rule).
