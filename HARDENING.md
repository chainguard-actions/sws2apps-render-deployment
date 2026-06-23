<!-- markdownlint-disable -->

# Hardening Report: sws2apps--render-deployment/v2.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sws2apps--render-deployment/v2.1.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

The 'Install Render CLI' step pipes a remotely fetched script directly to `sh` without first downloading and inspecting it: `curl -fsSL https://raw.githubusercontent.com/render-oss/cli/refs/tags/v${{ inputs.cli-version }}/bin/install.sh | sh`. This allows arbitrary code execution if the remote URL is compromised or if the `inputs.cli-version` value is manipulated.

Locations:

- `action.yaml:30`

### script-injection (severity: high)

Sub-rule (a): The expression `${{ inputs.cli-version }}` is interpolated directly inside a `run:` shell command string (line 30). An attacker-controlled value for `inputs.cli-version` can inject arbitrary shell commands. The offending line: `curl -fsSL https://raw.githubusercontent.com/render-oss/cli/refs/tags/v${{ inputs.cli-version }}/bin/install.sh | sh`

Locations:

- `action.yaml:30`

### script-injection (severity: high)

Sub-rule (a): The expression `${{ inputs.serviceId }}` is interpolated directly inside a `run:` shell command string (line 38) without quoting or env-var indirection. An attacker-controlled `serviceId` value can inject arbitrary shell commands. The offending line: `render deploys create ${{ inputs.serviceId }} --output text --confirm --wait`

Locations:

- `action.yaml:38`

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

**Fixes applied:** unsafe-shell, script-injection, static-inline-injection

**Notes:**

Fixed action.yaml: (1) Replaced `curl ... | sh` with a two-step approach: download the install script to /tmp/render-install.sh, execute it with `sh`, then delete it — eliminating the unsafe pipe-to-shell pattern. (2) Moved `${{ inputs.cli-version }}` into an env var `CLI_VERSION` and referenced it as `${CLI_VERSION}` in the shell script. (3) Moved `${{ inputs.serviceId }}` into an env var `SERVICE_ID` and referenced it as `"$SERVICE_ID"` (properly quoted) in the deploy command. These changes fix all unsafe-shell, script-injection, and static-inline-injection findings.

