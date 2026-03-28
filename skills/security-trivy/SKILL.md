---
name: security-trivy
description: Trivy for container images, filesystem (deps + IaC), SARIF, and SBOM. Use when configuring or fixing Trivy in local scripts or GitHub Actions.
---

# Trivy

## Install

- **mise:** `aqua:aquasecurity/trivy` with a concrete version in `.mise.toml` (pin releases that exist on GitHub).
- Verify: `trivy --version`

## Common commands

- **Filesystem (local / pre-push):**  
  `trivy fs --scanners vuln,misconfig,secret . --severity HIGH,CRITICAL`
- **Container image (after build):**  
  `trivy image --severity HIGH,CRITICAL <image-ref>`
- **SBOM:**  
  `trivy image --format cyclonedx -o sbom.json <image-ref>`

## CI

- Use **aquasecurity/trivy-action** with a **pinned** tag (not `@master`).
- Map `exit-code` to policy: e.g. `1` when `DANTE_LAUNCH_MODE=live`, `0` when advisory.
- Upload SARIF with `github/codeql-action/upload-sarif` (use `if: always()` when the scan step may fail).

## Placement

- **Fs scan:** local pre-push only when following single-placement gates.
- **Image scan:** `main` deploy pipeline after image is built and pushed to a registry.
