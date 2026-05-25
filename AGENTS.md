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
├── .github/
│   └── workflows/
│       ├── publish-claude-runtime.yml
│       └── publish-codex-runtime.yml
├── docker/
│   ├── claude.Dockerfile
│   ├── claude-entrypoint.sh
│   ├── codex.Dockerfile
│   └── codex-entrypoint.sh
├── docs/
│   ├── adr/
│   ├── runbooks/
│   └── changelog.md
├── README.md
├── AGENTS.md
└── CLAUDE.md -> AGENTS.md
```

## Runtime Images

### Codex Runtime

The first concrete image contract is the Codex Kubernetes runtime image.

- Dockerfile: `docker/codex.Dockerfile`
- Entrypoint: `docker/codex-entrypoint.sh`
- Base image: `ghcr.io/multica-ai/multica-backend:v0.3.6`
- Codex CLI package: `@openai/codex@0.133.0`
- Runtime sandbox package: Alpine `bubblewrap`
- Canonical image tag: `v0.3.6-codex-0.133.0-r2`
- Published image name: `ghcr.io/<owner>/multica-runtime-codex:v0.3.6-codex-0.133.0-r2`

The image must run as the non-root `multica` user and must not bake runtime secrets into the image. Runtime secrets belong in Kubernetes, Vault, or an equivalent runtime secret source.

### Claude Runtime

The second concrete image contract is the Claude Code Kubernetes runtime image.

- Dockerfile: `docker/claude.Dockerfile`
- Entrypoint: `docker/claude-entrypoint.sh`
- Base image: `ghcr.io/multica-ai/multica-backend:v0.3.6`
- Claude Code CLI package: `@anthropic-ai/claude-code@2.1.150`
- Canonical image tag: `v0.3.6-claude-2.1.150-r1`
- Published image name: `ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r1`

The image must run as the non-root `multica` user and must not bake runtime secrets into the image. Runtime secrets belong in Kubernetes, Vault, or an equivalent runtime secret source.

Do not add Kubernetes manifests, additional runtime images, new publish tags, or moving tags such as `latest` unless the image scope and release policy are explicitly updated.

Each runtime image has its own publish workflow. Runtime publish workflows must only trigger for changes to that runtime's Dockerfile, entrypoint, or workflow file; documentation-only changes must not publish images.

## Required Git Workflow for All Changes

These steps must be included in every implementation plan unless explicitly told otherwise:

- Create a feature branch from `main` using the `codex/` prefix
  - Example: `git checkout -b codex/<short-feature-name>`
- Keep commits focused and action-oriented
  - Example: `add codex runtime image scaffold`
- Do not bundle unrelated refactors with the main change
- Open a PR with a short summary and explicit test or validation notes
  - Example: `gh pr create --fill`

## Image Planning Guidance

- Keep documentation centered on Multica runtime images for Kubernetes-hosted agents.
- Prefer reusable runtime concerns such as shell environment, agent CLI configuration, Kubernetes access, GitOps tooling, and persistent config paths.
- Keep browser IDE, editor server, and workspace UI assumptions out of this repository.
- Put Dockerfiles under `docker/` because this repository may host multiple runtime images later.
- Keep image tags explicit and immutable unless an ADR changes the release policy.

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
