# Workspace repos (Current folder)

This document outlines the **top-level repos and folders** in the `Current` workspace and what each is used for. The workspace root is the parent of `cursor-config`; `.cursor` is a symlink to `cursor-config`.

## Top-level layout

| Repo / folder       | Purpose |
|--------------------|--------|
| **cursor-config**  | Shared Cursor configuration: rules, agents, skills, commands, hooks. Used as a git submodule at `.cursor` by consuming repos. See [cursor-config/README.md](README.md). |
| **dante-platform** | Product repo containing the main app and Cloudflare Worker. Sub-repos: `platform`, `cloudflare-ld-worker`. |
| **docs**           | Plans and documentation (e.g. `docs/plans/`). |
| **test-harness**   | Go BDD test harness for [Godog](https://github.com/cucumber/godog): Lambda and HTTP API step definitions, env init, scenario hooks. Consumed as a Go module by repos that run feature tests against deployed Lambdas/APIs. |
| **waypoint-framework** | Framework and tooling for Waypoint (schema-driven workflows, artifacts, CLI). Contains shared workflows, marketing-workflow infra, and the Waypoint CLI/app. |
| **waypoint-projects**  | Per-project Waypoint instances (forked workflows, artifacts, tasking). Each subfolder is a project that tracks a workflow from waypoint-framework. |

---

## cursor-config

- **Purpose:** Single source of truth for Cursor rules (`.mdc`), agents (`.md`), skills (`skills/*/SKILL.md`), commands (`commands/*.md`), and `hooks.json`.
- **Usage:** Either install skills via `npx skills add <org>/cursor-config`, or use as a git submodule at `.cursor` for full config.
- **Skills:** cli-qa-release, devops-secrets-auth, update-cursor-config (see [skills/README.md](skills/README.md)).

---

## dante-platform

Product repo; two main sub-repos:

| Path                     | Purpose |
|--------------------------|--------|
| **dante-platform/platform** | Main application: Vite, TypeScript, React, shadcn-ui, Tailwind CSS. |
| **dante-platform/cloudflare-ld-worker** | Cloudflare Worker for LaunchDarkly (feature flags / SDK). Wrangler deploy, Vitest. |

---

## test-harness

- **Purpose:** Reusable Go BDD harness for feature tests against **deployed** Lambdas and HTTP APIs (e.g. staging).
- **Consumers:** Repos that run Godog feature files; they keep conditioners and feature files locally and use this package for step logic, AWS/env setup, and logging.
- **Key packages:** `logger`, `env` (BaseEnvironment, InitEnvironment), `lambda` (Steps, FunctionURLRequest/Response), `api` (Steps, ExpectedResponse), `hooks` (scenario/step logging).
- **Docs:** [test-harness/README.md](../test-harness/README.md); cursor-config rule `rules/test-harness.mdc` for BDD conventions.

---

## waypoint-framework

Framework and tooling for Waypoint (schema-driven workflows, artifacts, CLI).

| Path | Purpose |
|------|--------|
| **waypoint-framework/waypoint** | Waypoint CLI (`@waypoint-framework/cli`), app, and marketing website. CLI: local validation and workflow versioning; consumed by workflow and project repos via mise/npm. |
| **waypoint-framework/software-workflow** | Schema-driven **software** workflow: waypoints, artifacts, samples, UI schemas. Obsidian vault (tasking). Consumed via Waypoint API by platform; projects fork this in waypoint-projects. |
| **waypoint-framework/marketing-workflow** | Schema-driven **marketing** workflow: waypoints, artifacts (e.g. media_plans, campaigns). Same layout as software-workflow but marketing-oriented; consumed by platform and marketing project forks. |
| **waypoint-framework/marketing-workflow-tools** | Infra and services for marketing workflow automation: Go Lambdas (e.g. audience-service), CDK in `infra/`. Uses mise; synth/diff for staging and live. |
| **waypoint-framework/git-figma-sync** | Figma plugin: syncs `designs/` from a waypoint-projects GitHub repo into the current Figma file (tokens → variables, screens → frames). GitHub token in plugin UI; build with npm. |

---

## waypoint-projects

Per-project Waypoint instances. Each subfolder is a **project** that has run `waypoint init --workflow <owner/repo>` and tracks a workflow from waypoint-framework.

| Path | Purpose |
|------|--------|
| **waypoint-projects/dante-platform** | Dante Platform project: fork of **software-workflow**. Designs, tasking, artifacts; consumed by dante-platform/platform via Waypoint API. |
| **waypoint-projects/ahara-mushrooms** | Ahara Mushrooms project: fork of **marketing-workflow**. Media plans, campaigns, branding, etc. |
| **waypoint-projects/legrandbus** | Legrand Bus project. |

New projects are added as new subfolders under `waypoint-projects/`, each with its own `.waypoint/version` pointing at the appropriate workflow repo.

---

## Quick reference

- **Cursor config:** `cursor-config` (or `.cursor` → cursor-config).
- **Main app:** `dante-platform/platform`.
- **Feature flags / LD:** `dante-platform/cloudflare-ld-worker`.
- **BDD / feature tests:** `test-harness` (Go module); feature files live in the repo under test.
- **Waypoint CLI:** `waypoint-framework/waypoint` (packages/cli); install via mise or `npm i @waypoint-framework/cli`.
- **Software workflow & tasking:** `waypoint-framework/software-workflow`; project fork: `waypoint-projects/dante-platform`.
- **Marketing workflow:** `waypoint-framework/marketing-workflow`; project fork: `waypoint-projects/ahara-mushrooms`.
- **Marketing Lambdas / CDK:** `waypoint-framework/marketing-workflow-tools`.
- **Figma ↔ Git sync:** `waypoint-framework/git-figma-sync`.
