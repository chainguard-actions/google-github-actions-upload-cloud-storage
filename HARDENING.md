<!-- markdownlint-disable -->

# Hardening Report: google-github-actions--upload-cloud-storage/v2.2.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **google-github-actions--upload-cloud-storage/v2.2.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command string. In integration.yml the step 'Get output' runs: `echo "${{ steps.upload.outputs.uploaded }}"`. The value of steps.upload.outputs.uploaded is substituted into the shell command before the shell ever sees it, allowing an attacker who can influence the uploaded output (e.g. via a crafted filename) to inject arbitrary shell commands.

Locations:

- `.github/workflows/integration.yml:51`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions or reusable workflows using mutable version tags instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved:
- draft-release.yml: `uses: 'google-github-actions/.github/.github/workflows/draft-release.yml@v3'`
- integration.yml: `uses: 'google-github-actions/auth@v2'`
- release.yml: `uses: 'google-github-actions/.github/.github/workflows/release.yml@v3'`
- unit.yml: `uses: 'google-github-actions/auth@v2'`

Locations:

- `.github/workflows/draft-release.yml:16`
- `.github/workflows/integration.yml:35`
- `.github/workflows/release.yml:10`
- `.github/workflows/unit.yml:47`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script injection in integration.yml by moving `${{ steps.upload.outputs.uploaded }}` to an env var `UPLOADED` and referencing it as `$UPLOADED` in the shell. Pinned all four unpinned action references to full commit SHAs: google-github-actions/auth@v2 → @c200f3691d83b41bf9bbd8638997a462592937ed (integration.yml and unit.yml), google-github-actions/.github workflows draft-release.yml@v3 and release.yml@v3 → @29c6d38eeb974133b4b66401985f7c70cf4a6681 (draft-release.yml and release.yml). Original tag names preserved in inline comments.

