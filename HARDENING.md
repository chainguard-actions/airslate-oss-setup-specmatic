<!-- markdownlint-disable -->

# Hardening Report: airslate-oss--setup-specmatic/v1.8.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **airslate-oss--setup-specmatic/v1.8.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of workflow_dispatch inputs inside run: shell commands. In the 'Tag new target' step, `${{ github.event.inputs.major_version }}` and `${{ github.event.inputs.target }}` are interpolated directly into the shell command `git tag -f ...`. In the 'Push new tag' step, `${{ github.event.inputs.major_version }}` is interpolated directly into `git push origin ... --force`. An attacker with write access (or a maintainer tricked into running the workflow) could supply values containing shell metacharacters to execute arbitrary commands. These inputs should be passed via env: variables and then referenced as quoted shell variables (e.g., `"$MAJOR_VERSION"`).

Locations:

- `.github/workflows/update-main-version.yml:37`
- `.github/workflows/update-main-version.yml:40`

### missing-permissions (severity: medium)

Workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g., write access to contents). A minimal permissions block (e.g., `permissions: read-all` or specific scopes) should be added.

Locations:

- `.github/workflows/basic-validation.yml:1`
- `.github/workflows/check-dist.yml:1`
- `.github/workflows/update-main-version.yml:1`
- `.github/workflows/versions.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script-injection in update-main-version.yml by moving workflow_dispatch inputs (major_version and target) from direct ${{ }} interpolation in run: commands into env: blocks, then referencing them as quoted shell variables ("$MAJOR_VERSION", "$TARGET"). Added permissions blocks to all 4 workflow files: contents: write for update-main-version.yml (needs to push tags), and contents: read for basic-validation.yml, check-dist.yml, and versions.yml (read-only workflows).

