---
name: backend-conventions
description: Backend service conventions — dependencies, composition root, feature flags, Lambda-style builds
---

# Backend Conventions

## Dependencies

- Add with the language's package manager (e.g. Go: `go get <package>`, then `go mod tidy`).
- For Lambda binaries: use the runtime-appropriate build (e.g. Go Lambda: `GOOS=linux GOARCH=amd64 CGO_ENABLED=0`, runtime `PROVIDED_AL2023`).

## Architecture

- All client initialization in the service entrypoint (e.g. `main.go`), dependencies injected at the root (single composition root).
- Shared packages live in `internal/`. Resource name constants in a central place (e.g. `internal/names/`).
- Prefer private struct fields (lowercase); expose behavior via methods or constructors (e.g. `NewCreator`), not direct field access.

## Feature Flags

- When the project uses feature flags (e.g. LaunchDarkly): initialize in the service entrypoint and close on shutdown.
- Feature-gated handlers use a flag check as a precondition. Return 404 or an appropriate disabled response when the flag is off.
