<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.57.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.57.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: script.sh uses unquoted shell variable expansions of workflow-controllable inputs. `${INPUT_SG_FLAGS}` (line 51) and `${INPUT_REVIEWDOG_FLAGS}` (line 60) are expanded without double-quotes in shell command position. These env vars are set from `inputs.sg_flags` and `inputs.reviewdog_flags` in action.yml's env: block. An attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) can break out of the intended command and execute arbitrary code. The shellcheck disable comment on line 50 acknowledges the unquoted expansion but does not mitigate the security risk. Fix: use the guarded form `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` or quote the variable, or use an array to pass flags.

Locations:

- `script.sh:51`
- `script.sh:60`

### missing-permissions (severity: medium)

None of the 5 workflow files define a top-level `permissions:` key, and no job within any of these files defines a job-level `permissions:` key. Without explicit permissions, workflows run with the default repository token permissions, which may be overly broad (e.g., write access to contents, pull-requests, etc.). Each workflow file should declare minimal required permissions.

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

Fixed two security findings: (1) script-injection in script.sh: replaced unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} with the guarded form ${VAR:+"$VAR"} to prevent shell metacharacter injection while preserving empty-value behavior; also removed the now-unnecessary shellcheck disable comment. (2) missing-permissions: added top-level permissions blocks to all 5 workflow files with minimal required permissions — depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), test.yml (contents:read, checks:write, pull-requests:write).

