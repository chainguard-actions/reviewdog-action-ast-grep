<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.60.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.60.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of workflow-controllable inputs appear in script.sh. On line 57, `${INPUT_SG_FLAGS}` (sourced from `inputs.sg_flags`) is passed unquoted to `ast-grep scan`, and on line 67, `${INPUT_REVIEWDOG_FLAGS}` (sourced from `inputs.reviewdog_flags`) is passed unquoted to `reviewdog`. An attacker-controlled caller can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) to achieve command injection. The `# shellcheck disable=SC2086` comment confirms the intentional word-splitting, but the safe guarded form `${VAR:+"$VAR"}` is not used.

Locations:

- `script.sh:57`
- `script.sh:67`

### missing-permissions (severity: medium)

None of the five workflow files define a top-level `permissions:` key, and no job within any of these files defines a job-level `permissions:` key. Without explicit permissions, workflows run with the default GitHub token permissions, which may be broader than necessary and increases the blast radius of a compromised step. Affected files: depup.yml, labels.yml, release.yml, reviewdog.yml, test.yml.

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

Fixed script-injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} (line 57) and ${INPUT_REVIEWDOG_FLAGS} (line 67) with properly tokenized bash arrays using the xargs-based approach (with empty-value guards). Each flags input is now safely split into an array and expanded with double-quoted array syntax. Added minimal permissions blocks to all 5 workflow files: depup.yml (contents: write, pull-requests: write), labels.yml (issues: write), release.yml (contents: write, pull-requests: write), reviewdog.yml (contents: read, checks: write, pull-requests: write), test.yml (contents: read, checks: write, pull-requests: write).

