---
description: Lint, build, and commit with conventional commit format
---

# /commit

1. Run the project's lint fix (e.g. `mise run lint:fix`)
2. If linter issues remain, fix them in code
3. Run the project's build to confirm compilation (e.g. `go build ./...`)
4. Stage all changes: `git add -A`
5. Commit with conventional commit format: `<type>: <description>` (all lowercase)
   - Determine the type from the changes: `feat`, `fix`, `refactor`, `perf`, `docs`, `test`, `chore`, `ci`
   - Write a concise description summarizing the "why"
