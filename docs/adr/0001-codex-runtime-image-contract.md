# ADR 0001: Codex Runtime Image Contract

## Status

Accepted

## Context

`multica-runtime` needs its first concrete runtime image for Kubernetes-hosted Multica agents. The first target is a Codex-based runtime that can run the Multica daemon against a self-hosted Multica backend without baking secrets into the image.

The Multica backend image `ghcr.io/multica-ai/multica-backend:v0.3.6` already contains the `multica` CLI at `/app/multica`. The OpenAI Codex CLI can be installed through npm as `@openai/codex`; version `0.133.0` was verified to expose a `codex` binary on Alpine. Codex on Linux expects `bubblewrap` on PATH for sandboxing, so the image includes Alpine's distro-managed `bubblewrap` package.

## Decision

Build the first image from `ghcr.io/multica-ai/multica-backend:v0.3.6`, install `@openai/codex@0.133.0`, and publish it as:

```text
ghcr.io/<owner>/multica-runtime-codex:v0.3.6-codex-0.133.0-r1
```

The `r2` image revision keeps the same Multica and Codex versions and adds Alpine `bubblewrap`:

```text
ghcr.io/<owner>/multica-runtime-codex:v0.3.6-codex-0.133.0-r2
```

Use an explicit immutable tag that includes the Multica version, Codex version, and image revision. Do not publish `latest` for the initial scaffold.

The image runs as the non-root `multica` user. Runtime secrets are required at container start through environment variables and are written only to the runtime user's Multica config file.

## Consequences

- Image rebuilds are reproducible against fixed Multica and Codex versions.
- Operators can see the key runtime compatibility versions from the tag.
- A future rebuild with the same Multica and Codex versions can increment the trailing image revision.
- Codex uses the OS-provided `bwrap` binary instead of warning and falling back to its bundled helper.
- Moving tags and Kubernetes manifests remain out of scope until a later release decision.
