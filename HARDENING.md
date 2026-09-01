<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

None of the workflow files have a top-level `permissions:` key, and no job within them defines job-level permissions. This means workflows run with the default (potentially broad) token permissions. Each workflow file should declare minimal required permissions.

Locations:

- `.github/workflows/depup.yml:1`
- `.github/workflows/labels.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Rule (b) violation: In script.sh, the env vars `${INPUT_SG_FLAGS}` (line 53) and `${INPUT_REVIEWDOG_FLAGS}` (line 62) are expanded **unquoted** in shell commands. Both are sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (set via the `env:` block in action.yml), making them workflow-controllable. Unquoted shell expansion allows an attacker to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via these inputs. The `# shellcheck disable=SC2086` comment on line 51 acknowledges the unquoted expansion but does not mitigate the injection risk. These should be quoted: `"${INPUT_SG_FLAGS}"` and `"${INPUT_REVIEWDOG_FLAGS}"`, or handled via an array.

Locations:

- `script.sh:53`
- `script.sh:62`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} expansions with bash arrays using xargs-based quote-aware tokenization. Added minimal top-level permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), test.yml (contents:read, checks:write, pull-requests:write).

