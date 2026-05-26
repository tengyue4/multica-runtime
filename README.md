# multica-runtime

Container images for running Multica agent runtimes in Kubernetes, with preinstalled AI coding CLIs and GitOps-friendly startup configuration.

## Runtime Images

### Codex Runtime Image

The Codex image is a Multica daemon runtime for self-hosted Multica backends.

- Dockerfile: `docker/codex.Dockerfile`
- Entrypoint: `docker/codex-entrypoint.sh`
- Base image: `ghcr.io/multica-ai/multica-backend:v0.3.6`
- Codex CLI: `@openai/codex@0.133.0`
- Runtime sandbox package: Alpine `bubblewrap`
- GitHub CLI package: Alpine `github-cli`
- Published tag: `ghcr.io/<owner>/multica-runtime-codex:v0.3.6-codex-0.133.0-r3`

The image runs as the non-root `multica` user, keeps `multica` and `codex` on `PATH`, and prepares writable runtime state under:

- `/home/multica/.multica`
- `/home/multica/multica_workspaces`

This repository intentionally does not include Kubernetes manifests yet. Runtime secrets should be provided by Kubernetes, Vault, or another external secret manager.

#### Build

```sh
docker build -f docker/codex.Dockerfile -t ghcr.io/tengyue4/multica-runtime-codex:v0.3.6-codex-0.133.0-r3 .
```

#### Smoke Tests

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

Verify the entrypoint fails fast when required runtime env is missing:

```sh
docker run --rm ghcr.io/tengyue4/multica-runtime-codex:v0.3.6-codex-0.133.0-r3
```

#### Runtime Configuration

Required environment variables:

- `MULTICA_SERVER_URL`
- `MULTICA_TOKEN`
- `OPENAI_API_KEY`

Optional environment variables:

- `MULTICA_APP_URL`, defaults to `MULTICA_SERVER_URL`
- `MULTICA_DAEMON_ID`, defaults to `HOSTNAME`
- `MULTICA_DAEMON_DEVICE_NAME`, defaults to `HOSTNAME`
- `MULTICA_AGENT_RUNTIME_NAME`, defaults to `K3s Codex Runtime`
- `MULTICA_DAEMON_MAX_CONCURRENT_TASKS`, defaults to `1`

At startup, the entrypoint writes `/home/multica/.multica/config.json` with `server_url`, `app_url`, and `token`, then starts:

```sh
multica daemon start --foreground --server-url "$MULTICA_SERVER_URL" --daemon-id "$MULTICA_DAEMON_ID" --device-name "$MULTICA_DAEMON_DEVICE_NAME" --runtime-name "$MULTICA_AGENT_RUNTIME_NAME" --max-concurrent-tasks "$MULTICA_DAEMON_MAX_CONCURRENT_TASKS" --no-auto-update
```

#### Publishing

The GitHub Actions workflow at `.github/workflows/publish-codex-runtime.yml` builds `docker/codex.Dockerfile` for `linux/amd64` and `linux/arm64`, logs in to GHCR with `GITHUB_TOKEN`, and publishes:

```text
ghcr.io/<owner>/multica-runtime-codex:v0.3.6-codex-0.133.0-r3
```

The workflow does not publish a moving `latest` tag.

### Claude Runtime Image

The Claude image is a Multica daemon runtime for self-hosted Multica backends using Claude Code.

- Dockerfile: `docker/claude.Dockerfile`
- Entrypoint: `docker/claude-entrypoint.sh`
- Base image: `ghcr.io/multica-ai/multica-backend:v0.3.6`
- Claude Code CLI: `@anthropic-ai/claude-code@2.1.150`
- GitHub CLI package: Alpine `github-cli`
- Published tag: `ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2`

The image runs as the non-root `multica` user, keeps `multica` and `claude` on `PATH`, and prepares writable runtime state under:

- `/home/multica/.multica`
- `/home/multica/multica_workspaces`

#### Build

```sh
docker build \
  -f docker/claude.Dockerfile \
  --build-arg CLAUDE_CODE_VERSION=2.1.150 \
  -t ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2 .
```

#### Pull

```sh
docker pull ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2
```

#### Smoke Tests

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

Verify the entrypoint fails fast when required runtime env is missing:

```sh
docker run --rm ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2
```

#### Runtime Configuration

Required environment variables:

- `MULTICA_SERVER_URL`
- `MULTICA_TOKEN`
- `ANTHROPIC_API_KEY`

Optional environment variables:

- `MULTICA_APP_URL`, defaults to `MULTICA_SERVER_URL`
- `MULTICA_DAEMON_ID`, defaults to `HOSTNAME`
- `MULTICA_DAEMON_DEVICE_NAME`, defaults to `HOSTNAME`
- `MULTICA_AGENT_RUNTIME_NAME`, defaults to `K3s Claude Runtime`
- `MULTICA_DAEMON_MAX_CONCURRENT_TASKS`, defaults to `1`

At startup, the entrypoint writes `/home/multica/.multica/config.json` with `server_url`, `app_url`, and `token`, then starts:

```sh
multica daemon start --foreground --server-url "$MULTICA_SERVER_URL" --daemon-id "$MULTICA_DAEMON_ID" --device-name "$MULTICA_DAEMON_DEVICE_NAME" --runtime-name "$MULTICA_AGENT_RUNTIME_NAME" --max-concurrent-tasks "$MULTICA_DAEMON_MAX_CONCURRENT_TASKS" --no-auto-update
```

#### Publishing

The GitHub Actions workflow at `.github/workflows/publish-claude-runtime.yml` builds `docker/claude.Dockerfile` for `linux/amd64` and `linux/arm64`, logs in to GHCR with `GITHUB_TOKEN`, and publishes:

```text
ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2
```

The workflow does not publish a moving `latest` tag.

## Runtime-Scoped Publishing

Each runtime image has its own publish workflow. Runtime image workflows trigger only when that runtime's Dockerfile, entrypoint, or workflow changes. Documentation-only changes do not publish images.

## Security Notes

- Do not put secrets in Dockerfiles, build args, workflow logs, README examples, or image labels.
- Provide runtime tokens and provider API keys only at runtime through the deployment platform.
- The image runs as a non-root user.
- Extra packages are limited to shell/runtime basics, Git/SSH, GitHub CLI, CA certificates, Node/npm for provider CLI installation, and Codex's distro-managed `bubblewrap` sandbox helper.
- Bundled tools and upstream base images keep their own licenses and terms. Review Multica, OpenAI Codex CLI, Anthropic Claude Code, GitHub CLI, Alpine, Node.js/npm, Git, OpenSSH, and related package terms before redistribution or production use.

## Contributor Guidance

`AGENTS.md` is the canonical shared contributor and agent guidance. `CLAUDE.md` is a compatibility symlink to the same guidance.

Meaningful repository changes should keep `README.md`, `AGENTS.md`, `docs/changelog.md`, ADRs, and runbooks aligned.
