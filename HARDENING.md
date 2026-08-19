<!-- markdownlint-disable -->

# Hardening Report: sws2apps--render-deployment/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sws2apps--render-deployment/v1.7.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

The workflow uses `permissions: read-all` at the top level, which grants overly broad read access across all scopes. It should be replaced with specific minimal permissions (e.g., `contents: read`) rather than the blanket `read-all` grant.

Locations:

- `.github/workflows/ci.yml:7`
- `.github/workflows/publish.yml:6`
- `.github/workflows/code-ql.yml:9`
- `.github/workflows/scorecards.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions

**Notes:**

Replaced `permissions: read-all` with specific minimal permissions in all four workflow files:
- ci.yml: `contents: read` (job already has its own permissions block with `contents: read` + `id-token: write`)
- publish.yml: `contents: write` (semantic-release needs to push tags/create releases; no job-level permissions block so top-level applies)
- code-ql.yml: `contents: read` (job already has its own permissions block with `actions: read`, `contents: read`, `security-events: write`)
- scorecards.yml: `contents: read` (job already has its own permissions block with `security-events: write`, `id-token: write`, `actions: read`, `contents: read`)

