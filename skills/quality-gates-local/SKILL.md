---
name: quality-gates-local
description: Single-placement local quality gates (Husky pre-commit/pre-push, verify-local.sh, DANTE_LAUNCH_MODE). Use when wiring or debugging typecheck, lint, secrets, Semgrep, Trivy fs without duplicating checks in CI.
---

# Local quality gates (single placement)

## Model

- **pre-commit:** typecheck, lint, TruffleHog (verified secrets).
- **pre-push:** Semgrep, Trivy filesystem (vuln + misconfig + secret scanners).
- **CI (PR):** Playwright (and other cloud-only checks) — **not** a second copy of local gates.
- **Trust boundary:** Intended for org-only repos; `git commit --no-verify` bypasses hooks. Document risk in repo `docs/quality-gates.md`.

## Source of truth

- One script (e.g. `scripts/verify-local.sh`) with phases `commit`, `push`, `all`.
- Husky hooks call `pnpm run verify:pre-commit` / `verify:pre-push` which invoke that script.
- `DANTE_LAUNCH_MODE=pre-launch` (default): Trivy fs non-blocking on HIGH/CRITICAL for exit code; Semgrep failures do not block push when not `live` (implement in script).
- `DANTE_LAUNCH_MODE=live`: Semgrep and Trivy fs must pass for push.

## Tooling

- Install CLIs with **mise** (see repo `.mise.toml`). **Python 3.12** is required for `pipx:semgrep` to avoid `pkg_resources` issues on Python 3.13+.

## Commands

```bash
mise install
pnpm install
pnpm run verify:local
```

## Reference layout

Example template: **`software-template/`** on branch **`software-template-work`** in the [software-workflow](https://github.com/waypoint-framework/software-workflow) repo. Check it out as a **sibling git worktree** (not nested under the main clone), e.g. `git worktree add ../software-template-worktree software-template-work` — then open `software-template/README.md` and `scripts/verify-local.sh`.
