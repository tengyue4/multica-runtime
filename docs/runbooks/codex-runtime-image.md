# Codex Runtime Image Runbook

This runbook covers building, smoke-testing, and publishing the Codex runtime image.

## Build Locally

From the repository root:

```sh
docker build \
  -f docker/codex.Dockerfile \
  -t ghcr.io/tengyue4/multica-runtime-codex:v0.3.6-codex-0.133.0-r3 .
```

## Smoke Test

Verify the Multica CLI:

```sh
docker run --rm --entrypoint multica ghcr.io/tengyue4/multica-runtime-codex:v0.3.6-codex-0.133.0-r3 version
```

Verify the Codex CLI:

```sh
docker run --rm --entrypoint codex ghcr.io/tengyue4/multica-runtime-codex:v0.3.6-codex-0.133.0-r3 --version
```

Verify the Codex sandbox helper:

```sh
docker run --rm --entrypoint bwrap ghcr.io/tengyue4/multica-runtime-codex:v0.3.6-codex-0.133.0-r3 --version
```

Verify the GitHub CLI:

```sh
docker run --rm --entrypoint gh ghcr.io/tengyue4/multica-runtime-codex:v0.3.6-codex-0.133.0-r3 --version
```

Verify required environment validation:

```sh
docker run --rm ghcr.io/tengyue4/multica-runtime-codex:v0.3.6-codex-0.133.0-r3
```

The container should exit before daemon startup and report the first missing required environment variable.

## Publish

The GitHub Actions workflow publishes on pushes to `main` that affect the image, workflow, or docs, and can also be run manually with `workflow_dispatch`.

The published image is:

```text
ghcr.io/<owner>/multica-runtime-codex:v0.3.6-codex-0.133.0-r3
```

The workflow uses `GITHUB_TOKEN` for GHCR authentication and does not require runtime secrets.

## Updating Versions

When changing the Multica or Codex version:

1. Verify the new base image and Codex install path locally.
2. Update `docker/codex.Dockerfile`.
3. Update the workflow `IMAGE_TAG`.
4. Update `README.md`, `AGENTS.md`, this runbook, `docs/changelog.md`, and the ADR if the policy changes.
5. Build and smoke-test locally before opening a PR.
