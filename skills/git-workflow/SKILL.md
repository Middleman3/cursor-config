---
name: git-workflow
description: Branch policy, conventional commits, PR workflow, CI monitoring, versioning
---

# Git Workflow

## Branching

- Always check current branch. If on `main`, create a feature branch immediately.
- Feature branches are the only place code changes happen.

## Conventional Commits

- All lowercase: `<type>: <description>`
- Types: `feat`, `fix`, `refactor`, `perf`, `docs`, `test`, `chore`, `ci`
- Examples: `feat: add preview endpoint`, `fix: handle empty request body`
- PR titles follow the same format, all lowercase.

## Commit Flow

1. Run the project's lint fix (e.g. `mise run lint:fix`) before committing.
2. Address any remaining linter issues.
3. Run the project's build to confirm compilation (e.g. `go build ./...`).
4. Stage and commit.

## PR Flow

1. Push with `-u` flag: `git push -u origin HEAD`
2. Create PR via `gh pr create` with title in conventional commit format.
3. PR body must follow `.github/pull_request_template.md` if present.
4. After push: monitor CI via `gh pr checks` or `gh run list --branch <branch>`.
5. On failure: inspect with `gh run view <run-id> --log-failed`.

## Versioning and Releases

- Every merge to `main` typically produces a semver git tag and a GitHub Release (project-dependent).
- `CHANGELOG.md` is updated with each release (Keep a Changelog format) when the project uses it.
- Version bump derived from conventional commit type: `feat:` = minor, `fix:` = patch, `!` suffix or `BREAKING CHANGE` in body = major.
