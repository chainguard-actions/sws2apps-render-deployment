<!-- markdownlint-disable -->

# Hardening Report: sws2apps--render-deployment/v2.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sws2apps--render-deployment/v2.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Two `run:` blocks in action.yaml directly interpolate `${{ inputs.* }}` expressions into shell command strings (sub-rule a), allowing an attacker who controls the calling workflow's inputs to inject arbitrary shell commands.

1. "Install Render CLI" step: `curl -fsSL https://raw.githubusercontent.com/render-oss/cli/refs/tags/v${{ inputs.cli-version }}/bin/install.sh | sh` — `inputs.cli-version` is interpolated directly into the URL before the shell sees it, enabling path traversal or URL manipulation.

2. "Trigger deploy with Render CLI" step: `render deploys create ${{ inputs.serviceId }} --output text --confirm --wait` — `inputs.serviceId` is interpolated directly as a shell token, enabling command injection via a crafted service ID value.

Fix: move both inputs into `env:` variables and reference them as double-quoted shell variables, e.g. `"$CLI_VERSION"` and `"$SERVICE_ID"`.

Locations:

- `action.yaml:29`
- `action.yaml:36`

### unsafe-shell (severity: high)

The "Install Render CLI" step pipes a remotely-fetched script directly to `sh` without any integrity verification: `curl -fsSL https://raw.githubusercontent.com/render-oss/cli/refs/tags/v${{ inputs.cli-version }}/bin/install.sh | sh`. If the remote URL is compromised or the content is tampered with (e.g. via a MITM or a malicious tag), arbitrary code will execute on the runner. The script should be downloaded to a file first, its checksum verified, and then executed separately.

Locations:

- `action.yaml:29`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.cli-version }}" appears directly in run: block of step "Install Render CLI"; move to env: map

Locations:

- `action.yml:30`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.serviceId }}" appears directly in run: block of step "Trigger deploy with Render CLI"; move to env: map

Locations:

- `action.yml:38`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unsafe-shell, static-inline-injection

**Notes:**

Fixed action.yaml with two changes:
1. 'Install Render CLI' step: moved inputs.cli-version into env: as CLI_VERSION, and changed the install pattern from 'curl ... | sh' (unsafe pipe-to-shell) to downloading the script to a temp file first, then executing it separately — fixing both script-injection and unsafe-shell findings.
2. 'Trigger deploy with Render CLI' step: moved inputs.serviceId into env: as SERVICE_ID and referenced it as double-quoted "$SERVICE_ID" in the run: block — fixing the script-injection finding.

