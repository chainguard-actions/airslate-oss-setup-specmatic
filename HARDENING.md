<!-- markdownlint-disable -->

# Hardening Report: airslate-oss--setup-specmatic--/v1.7.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **airslate-oss--setup-specmatic--/v1.7.4** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of workflow_dispatch inputs inside run: shell commands. In update-main-version.yml, `${{ github.event.inputs.major_version }}` and `${{ github.event.inputs.target }}` are interpolated directly into git commands without going through env: variables or any quoting. An attacker with write access (or a misconfigured workflow) could inject arbitrary shell commands. Offending lines: `run: git tag -f ${{ github.event.inputs.major_version }} ${{ github.event.inputs.target }}` and `run: git push origin ${{ github.event.inputs.major_version }} --force`.

Locations:

- `.github/workflows/update-main-version.yml:36`
- `.github/workflows/update-main-version.yml:39`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable version tags instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the tag is moved. Unpinned references: basic-validation.yml — actions/checkout@v6, actions/setup-node@v6, actions/cache@v5, codecov/codecov-action@v6; check-dist.yml — actions/checkout@v6, actions/setup-node@v6, actions/upload-artifact@v7; codeql-analysis.yml — actions/checkout@v6, github/codeql-action/init@v4, github/codeql-action/autobuild@v4, github/codeql-action/analyze@v4; update-main-version.yml — actions/checkout@v6; versions.yml — actions/checkout@v6, actions/setup-java@v5 (×2).

Locations:

- `.github/workflows/basic-validation.yml:49`
- `.github/workflows/check-dist.yml:33`
- `.github/workflows/codeql-analysis.yml:43`
- `.github/workflows/update-main-version.yml:27`
- `.github/workflows/versions.yml:52`

### missing-permissions (severity: medium)

Four workflow files have no top-level permissions: block and no job-level permissions: block on any of their jobs. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (which may be read/write), violating the principle of least privilege. Affected files: basic-validation.yml, check-dist.yml, update-main-version.yml, versions.yml.

Locations:

- `.github/workflows/basic-validation.yml:1`
- `.github/workflows/check-dist.yml:1`
- `.github/workflows/update-main-version.yml:1`
- `.github/workflows/versions.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three finding types across 5 workflow files: (1) script-injection in update-main-version.yml — moved github.event.inputs.major_version and github.event.inputs.target into env: blocks and referenced as shell variables; (2) unpinned-uses — pinned all 11 action references to full 40-char SHAs with tag comments (actions/checkout, actions/setup-node, actions/cache, codecov/codecov-action, actions/upload-artifact, github/codeql-action/{init,autobuild,analyze}, actions/setup-java); (3) missing-permissions — added top-level permissions blocks to basic-validation.yml (contents: read), check-dist.yml (contents: read), update-main-version.yml (contents: write, required for git push), and versions.yml (contents: read). codeql-analysis.yml already had a job-level permissions block so was not modified for that finding.

