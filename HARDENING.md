<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.60.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.60.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

None of the workflow files define a top-level or job-level `permissions:` block. Without explicit permissions, workflows inherit the default repository permissions (which may be broad), violating the principle of least privilege. All five workflow files are affected: reviewdog.yml, test.yml, release.yml, depup.yml, and labels.yml.

Locations:

- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/depup.yml:1`
- `.github/workflows/labels.yml:1`

### script-injection (severity: high)

Rule (b) violation: In script.sh, the variables `${INPUT_SG_FLAGS}` and `${INPUT_REVIEWDOG_FLAGS}` — which are set from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively in action.yml — are expanded unquoted inside shell commands. An attacker-controlled workflow can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) that will be interpreted by the shell. The shellcheck disable comment (SC2086) on line 46 acknowledges the unquoted expansion but does not mitigate the injection risk. Offending lines: `  ${INPUT_SG_FLAGS} |` and `    ${INPUT_REVIEWDOG_FLAGS} |`.

Locations:

- `script.sh:47`
- `script.sh:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script injection in script.sh by tokenizing INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS into bash arrays using xargs (quote-aware tokenization), replacing unquoted expansions with safe array expansions. Added minimal top-level permissions blocks to all 5 workflow files: reviewdog.yml (contents:read, checks:write, pull-requests:write), test.yml (contents:read, checks:write, pull-requests:write, security-events:write), release.yml (contents:write, pull-requests:write), depup.yml (contents:write, pull-requests:write), labels.yml (contents:read, issues:write).

