---
name: github-pr-tour
description: Add concise, lettered PR tour comments via gh (inline on the diff) so reviewers get a fast high-level map—use when opening or polishing a PR for review.
---

# GitHub PR tour comments

## Goal

Speed up review: a **short** set of **high-level** notes on the PR diff—not a comment on every hunk. Each note is labeled **Tour A**, **Tour B**, … so reviewers can search (`Tour A`) in **Files changed** and jump straight to intent.

## Style

- **Letters**, not numbers (easier to spot and grep).
- **One or two sentences** per tour stop.
- **Few anchors** (often 3–7): architecture, risky areas, how to run/test, rollout/flags—whatever actually needs context.
- Skip obvious diffs; the tour is the **map**, not a second code review.

## Where comments go

- **Inline** (recommended): show on **Files changed**. Use the GitHub API—comments only stick to **lines that appear in the PR diff** (additions/changes), not unchanged context.

```bash
gh pr view <N> --json headRefOid -q .headRefOid   # commit_id for HEAD
```

```bash
gh api --method POST repos/<owner>/<repo>/pulls/<N>/comments \
  -f body="**Tour A** — …" \
  -f commit_id="<headRefOid>" \
  -f path="path/to/file" \
  -F line=<line> \
  -f side=RIGHT
```

Pick `path` + `line` on a **changed** line (use the PR diff or `gh pr diff` to find).

- **Conversation** (optional): one **summary** for the whole PR: `gh pr comment <N> --body "…"`.

## Workflow

1. Push the branch; open PR (or use existing).
2. Delete stale tour comments if you are **replacing** a tour (delete old issue-thread comments and/or old review comments via `gh api` DELETE).
3. Add **Tour A**, **Tour B**, … on the most important **files/regions**—not every file.

## When not to use

Tiny one-file PRs, or when the PR description already says everything. Prefer a good PR description + 2–3 tour stops over noise.
