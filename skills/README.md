# Skills

Agent skills in this directory follow the [Agent Skills standard](https://agentskills.io) (SKILL.md with `name` and `description` frontmatter). They work with Cursor and the [skills CLI](https://skills.sh/docs/cli).

| Skill | Description |
|-------|-------------|
| **cli-qa-release** | Full CLI QA and release cycle — publish RCs, verify in consumer, revert pins, bump version, commit, push, tag; npm publish via GitHub Actions. Use when changing or validating CLI functionality or when releasing the CLI. |
| **devops-secrets-auth** | Authenticate with GitHub, npm, Figma, and other tools using the AWS Secrets Manager `devops` secret. Use when you need tokens for `gh`, npm publish, Figma API, or other devops-backed services. |
| **update-cursor-config** | Update Cursor configuration (rules, agents, hooks, skills, commands) and propagate to consuming repos. Use when changing cursor-config or adding hooks; requires consuming repos to update the cursor-config submodule. |
| **ui-prototyping** | Prototype UI in code with Cradle (SW + IndexedDB), forbid inline mocks, export BFF OpenAPI and third-party manifests into software-workflow artifacts. Use when vibecoding app UI without a real backend yet. |
| **internal-tools** | Set up `waypoint-framework/software-workflow/internal_tools` (task board manifest, UI viewer S3 `config.json` via AWS CLI). Follow each tool’s INIT.md. |

Each skill lives in a folder named after its `name` in SKILL.md and contains at least `SKILL.md`; optional subdirs: `scripts/`, `references/`, `assets/`.
