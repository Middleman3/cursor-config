---
name: secrets-trufflehog
description: TruffleHog for verified secret detection in git history and filesystem. Use when configuring pre-commit, tuning scope, or interpreting results.
---

# TruffleHog

## Install

- **mise:** `aqua:trufflesecurity/trufflehog` (pin version in `.mise.toml`).
- Verify: `trufflehog --version`

## Pre-commit (git source)

- Example:  
  `trufflehog git "file://$REPO_ROOT" --since-commit HEAD --only-verified --fail`
- Requires at least one commit; initialize git in new repos before first commit.

## Flags

- **`--only-verified`:** Reduces noise; aligns with “don’t block on unverified” policies.
- **`--fail`:** Exit non-zero when verified secrets are found.

## Monorepos

- Narrow paths with TruffleHog path filters or scan from subdirs if needed; keep scans fast for pre-commit.

## Placement

- **pre-commit** only under single-placement gates (not repeated in PR CI).
