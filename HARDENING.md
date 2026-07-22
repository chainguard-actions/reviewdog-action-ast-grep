<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.58.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.58.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

None of the 5 workflow files define a `permissions:` key — neither at the top level nor at the job level. Without explicit permissions, workflows inherit the default repository token permissions, which may be broader than necessary. All five files (depup.yml, labels.yml, release.yml, reviewdog.yml, test.yml) are affected.

Locations:

- `.github/workflows/depup.yml:1`
- `.github/workflows/labels.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Rule (b) violation: In script.sh, the env vars `${INPUT_SG_FLAGS}` and `${INPUT_REVIEWDOG_FLAGS}` — sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (set via the `env:` block in action.yml) — are expanded unquoted inside the shell command. An attacker-controlled caller can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, backticks, glob chars) that the shell will interpret, enabling command injection. The `# shellcheck disable=SC2086` comment confirms the unquoted expansion is intentional but does not mitigate the injection risk. Offending lines: `  ${INPUT_SG_FLAGS} |` and `    ${INPUT_REVIEWDOG_FLAGS} |`.

Locations:

- `script.sh:47`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} expansions with array-based expansion using 'read -ra ARRAY <<< "${VAR}"' and '"${ARRAY[@]}"'. Added minimal permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), test.yml (contents:read, checks:write, pull-requests:write).

