<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.58.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.58.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh (invoked by action.yml's run: step), the env vars ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} — sourced from inputs.sg_flags and inputs.reviewdog_flags respectively — are expanded unquoted inside shell commands. This allows a caller to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via these inputs. The `# shellcheck disable=SC2086` comment confirms the intentional word-splitting, but it does not prevent command injection. Offending lines: `  ${INPUT_SG_FLAGS} |` (line 57) and `    ${INPUT_REVIEWDOG_FLAGS} |` (line 67).

Locations:

- `script.sh:57`
- `script.sh:67`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no job within any of these files defines a job-level `permissions:` key. Without explicit permissions, workflows run with the default (potentially broad) token permissions. All five workflow files are affected: depup.yml, labels.yml, release.yml, reviewdog.yml, and test.yml.

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

Fixed script injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} expansions with bash arrays (read -ra into arrays, expanded as "${array[@]}"). Added minimal permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), test.yml (contents:read, checks:write, pull-requests:write).

