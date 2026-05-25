# multica-runtime

This file is the canonical shared agent and contributor contract for this repository.

`AGENTS.md` is the source of truth for both Codex and Claude guidance. `CLAUDE.md` is retained as a compatibility symlink to this file for tools that still look for that path.

Container image repository for Multica agent runtime environments.

## Repository Purpose

This repository is dedicated to image components used to build Multica runtime containers for Kubernetes-hosted agents.

The intended direction is an agent CLI runtime: preinstalled AI coding CLIs plus common development and operator tooling. It is not a browser IDE image, and repository changes should avoid assumptions tied to interactive web workspaces.

## Current Repository Structure

```text
multica-runtime/
├── docs/
│   └── changelog.md
├── README.md
├── AGENTS.md
└── CLAUDE.md -> AGENTS.md
```

Dockerfiles, GitHub Actions workflows, image tagging, and publishing policy are intentionally not defined yet. Add those only when the first Multica runtime image contract is concrete.

## Required Git Workflow for All Changes

These steps must be included in every implementation plan unless explicitly told otherwise:

- Create a feature branch from `main` using the `codex/` prefix
  - Example: `git checkout -b codex/<short-feature-name>`
- Keep commits focused and action-oriented
  - Example: `add runtime image bootstrap guidance`
- Do not bundle unrelated refactors with the main change
- Open a PR with a short summary and explicit test or validation notes
  - Example: `gh pr create --fill`

## Image Planning Guidance

Until the first image contract is defined:

- Do not add Dockerfiles, build workflows, publish jobs, versioning policy, or registry tags without an explicit image scope.
- Keep documentation centered on Multica runtime images for Kubernetes-hosted agents.
- Prefer reusable runtime concerns such as shell environment, agent CLI configuration, Kubernetes access, GitOps tooling, and persistent config paths.
- Keep browser IDE, editor server, and workspace UI assumptions out of this repository.

## Documentation Standards

Every meaningful image, workflow, release, or repository change should update the relevant docs:

1. `README.md` for repository purpose, image usage, and contributor entrypoint changes
2. `AGENTS.md` for the canonical shared agent, workflow, release, and contributor contract
3. `CLAUDE.md` only as the compatibility symlink path for tools that still expect it
4. `docs/changelog.md` for meaningful image, workflow, infrastructure, and documentation changes
5. `docs/runbooks/` when build, publish, release, or operator procedures become concrete or materially change
6. `docs/adr/` when architecture, tooling, image, or release decisions and tradeoffs are intentionally locked in

Keep `README.md`, `AGENTS.md`, runbooks, ADRs, and the changelog aligned with implemented behavior.

Self-evaluation checklist:

1. Did I make a decision between alternatives? If yes, add or update an ADR.
2. Did I add steps someone must repeat or troubleshoot? If yes, add or update a runbook.
3. Did anything meaningful change? If yes, update the changelog.
4. Did project conventions, tooling, release process, or repo context change? If yes, update `AGENTS.md`.
