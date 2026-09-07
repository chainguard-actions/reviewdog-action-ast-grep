<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, two workflow-controllable input variables are expanded unquoted in shell commands, allowing an attacker to inject shell metacharacters. `${INPUT_SG_FLAGS}` (line 52) is passed unquoted as flags to `ast-grep scan`, and `${INPUT_REVIEWDOG_FLAGS}` (line 61) is passed unquoted to `reviewdog`. Both variables are set from `${{ inputs.sg_flags }}` and `${{ inputs.reviewdog_flags }}` respectively in action.yml. A calling workflow can supply values containing `;`, `|`, `$(...)`, or other metacharacters to achieve command injection. The `# shellcheck disable=SC2086` comment on line 50 suppresses the shellcheck warning but does not fix the vulnerability. These should be quoted: `"${INPUT_SG_FLAGS}"` and `"${INPUT_REVIEWDOG_FLAGS}"` (or use array-based argument passing).

Locations:

- `script.sh:52`
- `script.sh:61`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` block, and no individual job within any of these files defines a `permissions:` block. Without explicit permissions, workflows run with the default token permissions (which may be read/write depending on repository settings), violating the principle of least privilege. Each workflow should declare minimal required permissions.

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

Fixed script-injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} with xargs-based bash array tokenization (using the guarded while/read/xargs pattern), then expanding them as "${sg_flags[@]}" and "${reviewdog_flags[@]}". Removed the # shellcheck disable=SC2086 comment. Added top-level permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), test.yml (contents:read, checks:write, pull-requests:write, security-events:write).

