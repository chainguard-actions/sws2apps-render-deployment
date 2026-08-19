<!-- markdownlint-disable -->

# Hardening Report: sws2apps--render-deployment/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sws2apps--render-deployment/v1.6.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

Four workflow files set `permissions: read-all` at the top level. The `read-all` shorthand grants read access to all scopes and is considered overly broad — it must be replaced with specific minimal permission scopes (e.g., `contents: read`, `actions: read`, etc.).

Locations:

- `.github/workflows/ci.yml:7`
- `.github/workflows/code-ql.yml:9`
- `.github/workflows/publish.yml:6`
- `.github/workflows/scorecards.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions

**Notes:**

Replaced `permissions: read-all` with specific minimal permissions in all four workflow files:
- ci.yml: `contents: read` (job already defines its own `contents: read` + `id-token: write`)
- code-ql.yml: `contents: read` (job already defines its own `actions: read`, `contents: read`, `security-events: write`)
- publish.yml: `contents: write` (semantic-release needs to create releases/tags; no job-level override)
- scorecards.yml: `contents: read` (job already defines its own `security-events: write`, `id-token: write`, `actions: read`, `contents: read`)

