# multica-runtime

Container images for running Multica agent runtimes in Kubernetes, with preinstalled AI coding CLIs and GitOps-friendly startup configuration.

## Status

This repository is in its initial documentation bootstrap phase. The first runtime image contract has not been defined yet, so there are no Dockerfiles, build workflows, publish jobs, or image versioning policy in this pass.

## Direction

The intended image family is an agent CLI runtime for Kubernetes-hosted Multica agents. Future images should focus on:

- AI coding CLIs and their persistent configuration paths
- Common development and operator tooling
- Kubernetes and GitOps-friendly runtime behavior
- Clear image contracts, validation, and release documentation once builds are introduced

Browser IDE and workspace UI assumptions are out of scope for this repository.

## Contributor Guidance

`AGENTS.md` is the canonical shared contributor and agent guidance. `CLAUDE.md` is a compatibility symlink to the same guidance.

Meaningful repository changes should keep `README.md`, `AGENTS.md`, and `docs/changelog.md` aligned.
