<!-- markdownlint-disable -->

# Hardening Report: google-github-actions--upload-cloud-storage/v2.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **google-github-actions--upload-cloud-storage/v2.2.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression `${{ steps.upload.outputs.uploaded }}` is directly interpolated inside a `run:` shell command string. The value flows through YAML template substitution before the shell sees it, allowing an attacker who can influence the step output to inject arbitrary shell commands. Offending line: `run: 'echo "${{ steps.upload.outputs.uploaded }}"'`. Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `env: { UPLOADED: '${{ steps.upload.outputs.uploaded }}' }` then `run: echo "$UPLOADED"`.

Locations:

- `.github/workflows/integration.yml:51`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions or reusable workflows by mutable version tags instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved. Failing references: (1) `google-github-actions/.github/.github/workflows/draft-release.yml@v3` in draft-release.yml; (2) `google-github-actions/auth@v2` in integration.yml; (3) `google-github-actions/.github/.github/workflows/release.yml@v3` in release.yml; (4) `google-github-actions/auth@v2` in unit.yml. Each should be pinned to a full SHA, e.g. `uses: google-github-actions/auth@<40-char-sha> # v2`.

Locations:

- `.github/workflows/draft-release.yml:17`
- `.github/workflows/integration.yml:35`
- `.github/workflows/release.yml:10`
- `.github/workflows/unit.yml:49`

### missing-permissions (severity: medium)

Two workflow files have no `permissions:` key at the top level and no `permissions:` key on any job, so they run with the default (potentially broad) token permissions. `draft-release.yml` delegates to a reusable workflow and passes `secrets.ACTIONS_BOT_TOKEN` without restricting the calling workflow's own token. `release.yml` similarly delegates without any permissions restriction. Both files should declare `permissions: {}` or the minimal required scopes at the top level.

Locations:

- `.github/workflows/draft-release.yml:1`
- `.github/workflows/release.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across four workflow files: (1) script-injection in integration.yml: moved `${{ steps.upload.outputs.uploaded }}` into an env block as UPLOADED and referenced it as $UPLOADED in the run step; (2) unpinned-uses: pinned google-github-actions/auth@v2 to SHA c200f3691d83b41bf9bbd8638997a462592937ed in integration.yml and unit.yml, and pinned google-github-actions/.github reusable workflows @v3 to SHA 29c6d38eeb974133b4b66401985f7c70cf4a6681 in draft-release.yml and release.yml; (3) missing-permissions: added `permissions: {}` at the top level of draft-release.yml and release.yml.

