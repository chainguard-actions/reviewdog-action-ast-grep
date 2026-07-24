<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.58.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.58.6** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Unquoted shell variable expansions of workflow-controllable inputs in script.sh. `${INPUT_SG_FLAGS}` (line 52) and `${INPUT_REVIEWDOG_FLAGS}` (line 62) are expanded without double-quotes. These env vars are populated from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (set in action.yml's `env:` block). An attacker-supplied value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) would be parsed by the shell, enabling command injection. The `# shellcheck disable=SC2086` comment on line 51 confirms the author intentionally suppressed the shellcheck warning for word-splitting, but this does not mitigate the injection risk.

Locations:

- `script.sh:52`
- `script.sh:62`

### missing-permissions (severity: medium)

None of the workflow files have a top-level `permissions:` key, and no individual job within any workflow defines a `permissions:` block. This means all jobs run with GitHub's default token permissions, which may be broader than necessary (e.g., write access to contents and pull requests). Each workflow file should declare minimal required permissions.

Locations:

- `.github/workflows/depup.yml:1`
- `.github/workflows/labels.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script-injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} with bash arrays (read -ra with empty-check guards), preventing shell metacharacter injection while preserving multi-flag functionality. Added minimal top-level permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), and test.yml (contents:read, checks:write, pull-requests:write).

