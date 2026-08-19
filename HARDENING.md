<!-- markdownlint-disable -->

# Hardening Report: sws2apps--render-deployment/v1.8.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sws2apps--render-deployment/v1.8.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

The workflow uses `permissions: read-all` at the top level, which grants overly broad read access to all scopes. This should be replaced with specific minimal permissions (e.g., `contents: read`) for each job.

Locations:

- `.github/workflows/ci.yml:8`
- `.github/workflows/code-ql.yml:10`
- `.github/workflows/publish.yml:6`
- `.github/workflows/scorecards.yml:7`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions

**Notes:**

Replaced `permissions: read-all` with specific minimal permissions in all four workflow files:
- ci.yml: Top-level changed to `contents: read`; job already had its own `contents: read` + `id-token: write` block.
- code-ql.yml: Top-level changed to `contents: read`; job already had its own `actions: read`, `contents: read`, `security-events: write` block.
- publish.yml: Top-level changed to `contents: read`; added a job-level `permissions` block with `contents: write` and `id-token: write` (needed by semantic-release to create releases/tags and publish).
- scorecards.yml: Top-level changed to `contents: read`; job already had its own `security-events: write`, `id-token: write`, `actions: read`, `contents: read` block.

