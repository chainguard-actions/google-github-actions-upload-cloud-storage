<!-- markdownlint-disable -->

# Hardening Report: google-github-actions--upload-cloud-storage/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **google-github-actions--upload-cloud-storage/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a run: shell command. The step `run: 'echo "${{ steps.upload.outputs.uploaded }}"'` embeds `steps.upload.outputs.uploaded` (a workflow-controllable context) directly into the shell command string before the shell ever sees it. An attacker who can influence the uploaded output value could inject arbitrary shell commands. The value should be passed via an env: variable and then referenced as a quoted shell variable instead.

Locations:

- `.github/workflows/integration.yml:50`

### unpinned-uses (severity: high)

Multiple workflow files reference actions/reusable workflows using mutable version tags (@v3) instead of immutable full 40-character commit SHAs. This exposes the workflows to supply-chain attacks if the tag is moved to a malicious commit. Failing references: draft-release.yml: `google-github-actions/.github/.github/workflows/draft-release.yml@v3`; integration.yml: `google-github-actions/auth@v3`; release.yml: `google-github-actions/.github/.github/workflows/release.yml@v3`; unit.yml: `google-github-actions/auth@v3`. Each should be pinned to a full SHA (e.g. `google-github-actions/auth@<40-hex-char-sha> # v3`).

Locations:

- `.github/workflows/draft-release.yml:18`
- `.github/workflows/integration.yml:33`
- `.github/workflows/release.yml:10`
- `.github/workflows/unit.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed all findings: (1) script-injection in integration.yml line 50 — moved `${{ steps.upload.outputs.uploaded }}` into an env: variable `UPLOADED` and referenced it as `$UPLOADED` in the run command; (2) unpinned-uses — pinned google-github-actions/auth@v3 to SHA 7c6bc770dae815cd3e89ee6cdf493a5fab2cc093 in both integration.yml and unit.yml, and pinned the google-github-actions/.github reusable workflows (draft-release.yml and release.yml) to SHA 29c6d38eeb974133b4b66401985f7c70cf4a6681 in draft-release.yml and release.yml respectively.

