---
name: security-snyk
description: Snyk SCA for Node/npm/pnpm — auth, severity thresholds, scheduled scans vs PR. Use when wiring SNYK_TOKEN, snyk/actions, or comparing Snyk vs Trivy coverage.
---

# Snyk (SCA)

## Auth

- **GitHub Actions:** secret `SNYK_TOKEN` (from Snyk account).
- **Local (optional):** `snyk auth` or `SNYK_TOKEN` in env — dev-only; not a second required gate if Snyk runs on schedule in CI.

## CI

- **snyk/actions/node** with args e.g. `--severity-threshold=high`.
- **Scheduled + workflow_dispatch** avoids duplicating Snyk on every push when using single-placement gates.
- **`continue-on-error`:** `true` when `DANTE_LAUNCH_MODE != 'live'` (advisory); `false` or omit when you want failures to block in live mode.

## Relationship to Trivy

- **Snyk:** strong for dependency CVEs, policies, and PR integrations.
- **Trivy:** filesystem and image OS/library CVEs, IaC misconfig, SBOM — run in complementary slots (see repo quality-gates doc).
