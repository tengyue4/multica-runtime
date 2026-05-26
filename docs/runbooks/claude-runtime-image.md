# Claude Runtime Image Runbook

This runbook covers building, smoke-testing, and publishing the Claude runtime image.

## Build Locally

From the repository root:

```sh
docker build \
  -f docker/claude.Dockerfile \
  --build-arg CLAUDE_CODE_VERSION=2.1.150 \
  -t ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2 .
```

## Smoke Test

Verify the Multica CLI:

```sh
docker run --rm --entrypoint multica ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2 version
```

Verify the Claude Code CLI:

```sh
docker run --rm --entrypoint claude ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2 --version
```

Verify the GitHub CLI:

```sh
docker run --rm --entrypoint gh ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2 --version
```

Verify required environment validation:

```sh
docker run --rm ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2
```

The container should exit before daemon startup and report the first missing required environment variable.

## Pull

```sh
docker pull ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2
```

## Publish

The GitHub Actions workflow publishes on pushes to `main` that affect the Claude Dockerfile, Claude entrypoint, or Claude workflow. It can also be run manually with `workflow_dispatch`.

The published image is:

```text
ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2
```

The workflow uses `GITHUB_TOKEN` for GHCR authentication and does not require runtime secrets.

## Updating Versions

When changing the Multica or Claude Code version:

1. Verify the new base image and Claude Code install path locally.
2. Update `docker/claude.Dockerfile`.
3. Update the workflow `IMAGE_TAG`.
4. Update `README.md`, `AGENTS.md`, this runbook, `docs/changelog.md`, and the ADR if the policy changes.
5. Build and smoke-test locally before opening a PR.
