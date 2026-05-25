# Changelog

## 2026-05-25 - Codex Runtime Image Scaffold

- Added the first Multica runtime image contract for a Codex-based Kubernetes runtime.
- Added `docker/codex.Dockerfile` and `docker/codex-entrypoint.sh` for a non-root Multica daemon image based on `ghcr.io/multica-ai/multica-backend:v0.3.6` with `@openai/codex@0.133.0`.
- Added a GHCR publish workflow for `ghcr.io/<owner>/multica-runtime-codex:v0.3.6-codex-0.133.0-r1`.
- Documented build, smoke-test, runtime environment, security, runbook, and ADR guidance for the first image.

## 2026-05-24 - Bootstrap Repository Guidance

- Added canonical shared contributor and agent guidance for the Multica runtime image repository.
- Documented that the current pass is guidance-only and intentionally defers Dockerfiles, build workflows, image publishing, and versioning policy until the first runtime image contract is defined.
- Clarified that future work should focus on Kubernetes-hosted agent CLI runtimes and avoid browser IDE assumptions.
