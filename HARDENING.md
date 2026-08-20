<!-- markdownlint-disable -->

# Hardening Report: google-github-actions--upload-cloud-storage/v2.2.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **google-github-actions--upload-cloud-storage/v2.2.4** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions or reusable workflows using mutable version tags instead of full 40-character commit SHA digests, making them vulnerable to supply-chain attacks if the tag is moved:
- `.github/workflows/draft-release.yml` line 17: `uses: 'google-github-actions/.github/.github/workflows/draft-release.yml@v3'`
- `.github/workflows/integration.yml` line 40: `uses: 'google-github-actions/auth@v2'`
- `.github/workflows/release.yml` line 11: `uses: 'google-github-actions/.github/.github/workflows/release.yml@v3'`
- `.github/workflows/unit.yml` line 40: `uses: 'google-github-actions/auth@v2'`
All four are marked `# ratchet:exclude` but remain unpinned. Each should be replaced with a full SHA pin.

Locations:

- `.github/workflows/draft-release.yml:17`
- `.github/workflows/integration.yml:40`
- `.github/workflows/release.yml:11`
- `.github/workflows/unit.yml:40`

### script-injection (severity: high)

Sub-rule (a): A `run:` block in `.github/workflows/integration.yml` directly interpolates a GitHub Actions expression into a shell command string. The offending line is:
  `run: 'echo "${{ steps.upload.outputs.uploaded }}"'`
The value `steps.upload.outputs.uploaded` is a `steps.*.outputs.*` context value that flows through YAML template substitution before the shell sees it. An attacker who can influence the uploaded output (e.g. via a crafted filename) could inject shell metacharacters. The expression should be moved to an `env:` variable and the env var should be double-quoted in the shell command: `run: 'echo "$UPLOADED"'` with `env: { UPLOADED: '${{ steps.upload.outputs.uploaded }}' }`.

Locations:

- `.github/workflows/integration.yml:59`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all four unpinned-uses findings by resolving mutable tags to full 40-character commit SHAs: google-github-actions/.github@v3 → 29c6d38eeb974133b4b66401985f7c70cf4a6681 (used in draft-release.yml and release.yml), and google-github-actions/auth@v2 → c200f3691d83b41bf9bbd8638997a462592937ed (used in integration.yml and unit.yml). Fixed the script-injection finding in integration.yml by moving the `${{ steps.upload.outputs.uploaded }}` expression into an `env:` block as `UPLOADED` and referencing it as `$UPLOADED` in the shell run command.

