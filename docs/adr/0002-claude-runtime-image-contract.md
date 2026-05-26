# ADR 0002: Claude Runtime Image Contract

## Status

Accepted

## Context

`multica-runtime` now hosts a Codex runtime image and needs a matching Claude Code runtime image for Kubernetes-hosted Multica agents. The image should follow the existing runtime structure while keeping provider-specific startup and publishing isolated.

The Multica backend image `ghcr.io/multica-ai/multica-backend:v0.3.6` already contains the `multica` CLI at `/app/multica`. Claude Code can be installed through npm as `@anthropic-ai/claude-code`; version `2.1.150` was verified to expose a `claude` binary on Alpine.

## Decision

Build the Claude image from `ghcr.io/multica-ai/multica-backend:v0.3.6`, install `@anthropic-ai/claude-code@2.1.150`, and publish it as:

```text
ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r1
```

The `r2` image revision keeps the same Multica and Claude Code versions and adds Alpine `github-cli` so `gh` is available without baking any GitHub authentication into the image:

```text
ghcr.io/tengyue4/multica-runtime-claude:v0.3.6-claude-2.1.150-r2
```

Use an explicit immutable tag that includes the Multica version, Claude Code version, and image revision. Do not publish `latest`.

Keep a separate Claude publish workflow that triggers only when the Claude Dockerfile, Claude entrypoint, or Claude workflow changes.

## Consequences

- Claude image rebuilds are reproducible against fixed Multica and Claude Code versions.
- Operators can see the key runtime compatibility versions from the tag.
- Codex changes do not publish Claude images, and Claude changes do not publish Codex images.
- Documentation-only changes do not publish images.
- GitHub CLI auth remains runtime/user-managed; the image only provides the `gh` binary.
- Moving tags and Kubernetes manifests remain out of scope until a later release decision.
