<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.58.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **reviewdog--action-ast-grep/v1.58.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, the shell variables ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} are expanded unquoted inside run commands. These variables are set from inputs.sg_flags and inputs.reviewdog_flags (workflow-controllable data via the action.yml env: block), so an attacker can inject shell metacharacters (;, |, &, $(...), etc.) through these inputs. The shellcheck disable comment (# shellcheck disable=SC2086) even acknowledges the unquoted expansion. Offending lines: `${INPUT_SG_FLAGS} |` and `${INPUT_REVIEWDOG_FLAGS} |`.

Locations:

- `script.sh:47`
- `script.sh:56`

### missing-permissions (severity: medium)

None of the workflow files under .github/workflows/ define a top-level `permissions:` key, and no job within them defines job-level permissions either. Without explicit permissions, workflows run with the default token permissions (which may be write-all depending on repository settings), violating the principle of least privilege. Affected files: depup.yml, labels.yml, release.yml, reviewdog.yml, test.yml.

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

Fixed script-injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} expansions with properly quoted array expansions using 'read -ra'. Removed the shellcheck disable comment. Added top-level permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), test.yml (contents:read, checks:write, pull-requests:write).

