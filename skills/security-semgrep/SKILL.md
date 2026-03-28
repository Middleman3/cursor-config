---
name: security-semgrep
description: Semgrep OSS SAST — configs, exit codes, and local vs CI placement. Use when adding rules, fixing findings, or wiring Semgrep into hooks.
---

# Semgrep (OSS)

## Install

- **mise:** `python` 3.12+, `pipx`, and `pipx:semgrep` pinned to a release (e.g. `1.156.0`). Newer Python 3.13-only venvs can break older Semgrep/OpenTelemetry deps; keep Python 3.12 for pipx Semgrep.
- Verify: `semgrep --version`

## Common usage

- **Auto rules:**  
  `semgrep --config=auto --error --quiet`
- **Custom config:**  
  `semgrep --config=.semgrep.yml --error`

## Placement

- **pre-push** only when using single-placement gates (not duplicated on PR CI).
- In **live** mode, findings should block push (`--error` exits non-zero). In **pre-launch**, shell can ignore non-zero exit if policy is advisory.

## Fixing findings

- Prefer **autofix** hints from Semgrep output when safe.
- For Dockerfile `USER`: run as non-root (`USER node` after `chown`) to satisfy `dockerfile.security.missing-user`.
