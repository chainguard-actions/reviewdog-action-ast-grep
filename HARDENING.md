<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.58.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.58.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansions of workflow-controllable inputs in script.sh. `${INPUT_SG_FLAGS}` (line ~46) and `${INPUT_REVIEWDOG_FLAGS}` (line ~55) are expanded without double-quotes in shell commands. These env vars are populated from `inputs.sg_flags` and `inputs.reviewdog_flags` (both optional, default '') via the action.yml env: block. An attacker-controlled caller can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) that will be parsed by the shell. The `# shellcheck disable=SC2086` comment even acknowledges the unquoted expansion. The safe form for optional positional arguments is `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` and `${INPUT_REVIEWDOG_FLAGS:+"$INPUT_REVIEWDOG_FLAGS"}`.

Locations:

- `script.sh:46`
- `script.sh:55`

### missing-permissions (severity: medium)

None of the 5 workflow files under .github/workflows/ define a top-level `permissions:` key, and no job in any of these files defines a job-level `permissions:` key. Without explicit permissions, workflows run with the repository's default token permissions, which may be overly broad (e.g., write access to contents, pull-requests, etc.). Affected files: depup.yml, labels.yml, release.yml, reviewdog.yml, test.yml.

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

Fixed script-injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} (line 46) and ${INPUT_REVIEWDOG_FLAGS} (line 55) with the safe ${VAR:+"$VAR"} form, which drops the argument when empty and double-quotes it when non-empty, preventing shell metacharacter injection. Removed the now-unnecessary `# shellcheck disable=SC2086` comment. Added top-level `permissions:` blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (checks:write, contents:read, pull-requests:write), and test.yml (checks:write, contents:read, pull-requests:write).

