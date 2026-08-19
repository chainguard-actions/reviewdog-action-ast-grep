<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.57.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.57.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of workflow-controllable inputs appear in script.sh (called from action.yml's composite `run:` step). `${INPUT_SG_FLAGS}` (line 57) and `${INPUT_REVIEWDOG_FLAGS}` (line 67) are expanded without double-quoting or the safe guarded form `${VAR:+"$VAR"}`. These variables hold the `inputs.sg_flags` and `inputs.reviewdog_flags` values set by the calling workflow, allowing an attacker to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via those inputs. The shellcheck suppression comment `# shellcheck disable=SC2086` on line 53 confirms the author is aware of the unquoted expansion but has not applied the safe guarded form.

Locations:

- `script.sh:57`
- `script.sh:67`

### missing-permissions (severity: medium)

None of the five workflow files define a `permissions:` key at the top level or at the job level. Without explicit permissions, workflows run with the default token permissions (which may include `write` access to contents, pull-requests, etc.), violating the principle of least privilege. All five files are affected: depup.yml, labels.yml, release.yml, reviewdog.yml, and test.yml.

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

Fixed script-injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} with the safe guarded form ${VAR:+"$VAR"} to prevent shell metacharacter injection while correctly handling empty optional inputs. Removed the shellcheck disable=SC2086 comment that was suppressing the warning. Added minimum-privilege permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), and test.yml (contents:read, checks:write, pull-requests:write).

