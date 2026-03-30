---
name: pr-judgement
description: Assess a GitHub PR for direction, risk, scope, and merge readiness — use when asked to judge a PR, review merge risk, or decide if a change set is coherent before merge.
---

# PR judgement

Structured review of a pull request **without** replacing line-by-line code review. Produces a verdict reviewers can use to decide merge, request changes, or rebase.

## When to use

- User provides a PR URL or `owner/repo` + number.
- Questions like “is this a good direction?”, “merge risk?”, “redundant work?”, “stale PR?”

## Prerequisites

- [GitHub CLI](https://cli.github.com/) (`gh`) authenticated for the repo.

## Workflow

1. **Resolve the PR**  
   From a URL or args, run:
   ```bash
   gh pr view <N> --repo <owner>/<repo> --json title,body,state,baseRefName,headRefName,url,commits,files,additions,deletions
   ```

2. **Measure branch freshness** (critical for merge judgement):
   ```bash
   git fetch origin pull/<N>/head:pr-<N>   # optional local ref
   git fetch origin <baseBranch>
   git merge-base origin/<baseBranch> pr-<N>
   git rev-list --count origin/<baseBranch>..pr-<N>   # commits only on PR
   git rev-list --count pr-<N>..origin/<baseBranch>   # commits on base not in PR
   ```
   If the second count is large, call out **stale branch**: expect conflicts and possible semantic drift (the PR may reintroduce removed code or miss security fixes).

3. **Scan the change set**  
   - `gh pr diff <N> --repo ... --name-only`  
   - Skim high-risk paths: `infra/`, auth/secrets, CI, `go.mod`/deps, migrations.

4. **Apply the rubric** (below) and write the report.

## Rubric

| Area | What to check |
|------|----------------|
| **Intent vs title/body** | Does the title match the diff? Is the description filled in or still a template? |
| **Scope** | Single coherent theme or mixed unrelated changes? |
| **Infra / breaking** | CDK or API changes that need deploy order, new secrets, or rollback notes? |
| **Security** | Secrets, auth headers, injection (e.g. GAQL/campaign IDs), overly broad IAM. |
| **Tests** | Coverage for new behavior; skipped scenarios (`@skip`); CI alignment. |
| **Dependencies** | New third-party SDKs; private modules; version pins. |
| **Tech debt** | Hardcoded env/customer IDs, TODO/WIP in production paths, error leakage to clients. |
| **Merge readiness** | Conflicts likely? Base branch policy? Release notes / CHANGELOG? |

## Output format

Use this structure (markdown):

1. **Verdict** — One paragraph: approve with nits / request changes / do not merge until …  
2. **Branch health** — Ahead/behind counts; stale or clean.  
3. **Alignment** — Title/body vs actual changes.  
4. **Strengths** — Bullet list.  
5. **Risks & gaps** — Bullet list (ordered by severity).  
6. **Suggested follow-ups** — Short, actionable (rebase, split PR, fill template, unskip tests, etc.).

## Do not

- Replace team security or compliance review for regulated changes.
- Post to GitHub unless the user asks to leave a review comment.
