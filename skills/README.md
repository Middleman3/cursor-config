# Skills

Agent skills in this directory follow the [Agent Skills standard](https://agentskills.io) (SKILL.md with `name` and `description` frontmatter). They work with Cursor and the [skills CLI](https://skills.sh/docs/cli).

| Skill | Description |
|-------|-------------|
| **cli-qa-release** | Full CLI QA and release cycle — publish RCs, verify in consumer, revert pins, bump version, commit, push, tag; npm publish via GitHub Actions. Use when changing or validating CLI functionality or when releasing the CLI. |
| **devops-secrets-auth** | Authenticate with GitHub, npm, Figma, and other tools using the AWS Secrets Manager `devops` secret. Use when you need tokens for `gh`, npm publish, Figma API, or other devops-backed services. |
| **update-cursor-config** | Update Cursor configuration (rules, agents, hooks, skills, commands) and propagate to consuming repos. Use when changing cursor-config or adding hooks; requires consuming repos to update the cursor-config submodule. |
| **internal-tools** | Set up `waypoint-framework/software-workflow/internal_tools` (task board manifest, UI viewer S3 `config.json` via AWS CLI). Follow each tool’s INIT.md. |
| **quality-gates-local** | Single-placement Husky gates (`verify-local.sh`, pre-commit vs pre-push, `DANTE_LAUNCH_MODE`). Use when wiring local checks without duplicating them in CI. |
| **security-trivy** | Trivy fs/image scans, SARIF, SBOM, mise install; alignment with pre-push vs main pipeline. |
| **security-semgrep** | Semgrep OSS — configs, Python 3.12 + pipx, exit codes, Dockerfile and rule fixes. |
| **secrets-trufflehog** | TruffleHog verified secrets, git vs fs, pre-commit scope. |
| **security-snyk** | Snyk SCA, `SNYK_TOKEN`, scheduled workflows, vs Trivy. |
| **playwright-ci-sharding** | Sharded E2E, blob reporter, `merge-reports`, artifacts. |
| **prototyping** | Prototype UIs with Cradle (`file:` deps, `VITE_CRADLE`, Playwright `E2E_CRADLE` for virtual vs real API). |
| **github-actions-oidc-aws** | OIDC to AWS, ECR, ECS render/deploy, CodeDeploy canary inputs. |
| **launchdarkly-ops** | Flag lifecycle, kill switch env, coordination with deploy canary; complements `/flag-toggle`. |
| **github-pr-tour** | Lettered **Tour A/B/…** PR comments via `gh` (inline on diff); high-level, concise—streamline review. |

Each skill lives in a folder named after its `name` in SKILL.md and contains at least `SKILL.md`; optional subdirs: `scripts/`, `references/`, `assets/`.
