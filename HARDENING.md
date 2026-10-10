<!-- markdownlint-disable -->

# Hardening Report: sws2apps--render-deployment/v2.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sws2apps--render-deployment/v2.1.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

The 'Install Render CLI' step pipes a remote script directly to `sh` without first downloading and inspecting it. The pattern `curl -fsSL https://raw.githubusercontent.com/render-oss/cli/refs/tags/v${{ inputs.cli-version }}/bin/install.sh | sh` executes whatever content is served at that URL immediately in the runner shell. An attacker who can influence the URL (via the `cli-version` input) or who compromises the remote host can execute arbitrary code. The script should be downloaded to a file, verified (e.g. checksum), and then executed separately.

Locations:

- `action.yaml:30`

### script-injection (severity: high)

Sub-rule (a): `${{ inputs.cli-version }}` is interpolated directly inside a `run:` shell command string (embedded in the curl URL). GitHub Actions performs template substitution before the shell ever sees the string, so a malicious value for `cli-version` (e.g. containing shell metacharacters) can break out of the URL context and inject arbitrary shell commands. The value must be passed via an `env:` variable and then double-quoted in the script. Offending line: `curl -fsSL https://raw.githubusercontent.com/render-oss/cli/refs/tags/v${{ inputs.cli-version }}/bin/install.sh | sh`

Locations:

- `action.yaml:30`

### script-injection (severity: high)

Sub-rule (a): `${{ inputs.serviceId }}` is interpolated directly inside a `run:` shell command string. GitHub Actions performs template substitution before the shell parses the command, so a malicious `serviceId` value containing shell metacharacters (e.g. `;`, `$(...)`, `|`) can inject arbitrary shell commands. The value must be passed via an `env:` variable and then double-quoted in the script. Offending line: `render deploys create ${{ inputs.serviceId }} --output text --confirm --wait`

Locations:

- `action.yaml:37`

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

Fixed action.yaml:
1. unsafe-shell: Changed 'Install Render CLI' step to download the install script to a temp file using `curl -fsSL ... -o "$INSTALL_SCRIPT"` and then execute it separately with `sh "$INSTALL_SCRIPT"`, instead of piping directly from the internet.
2. script-injection / static-inline-injection (cli-version): Moved `${{ inputs.cli-version }}` to an `env:` variable `CLI_VERSION` and referenced it as `${CLI_VERSION}` inside the double-quoted curl URL.
3. script-injection / static-inline-injection (serviceId): Moved `${{ inputs.serviceId }}` to an `env:` variable `SERVICE_ID` and referenced it as `"$SERVICE_ID"` (double-quoted) in the render deploy command.

