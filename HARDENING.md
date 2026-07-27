<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.59.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.59.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### permissions (severity: medium)

missing-permissions: None of the workflow files have a top-level `permissions:` key, and no job within any workflow defines its own `permissions:` block. This means workflows run with the default (potentially broad) token permissions. Affected files: depup.yml, labels.yml, release.yml, reviewdog.yml, test.yml.

Locations:

- `.github/workflows/depup.yml:1`
- `.github/workflows/labels.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Rule (b) violation: In script.sh, the env vars `${INPUT_SG_FLAGS}` and `${INPUT_REVIEWDOG_FLAGS}` — sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` in action.yml — are expanded **unquoted** in shell command lines. An attacker-controlled caller can inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) through these inputs. Offending lines: `${INPUT_SG_FLAGS} |` (passed as unquoted positional args to `ast-grep scan`) and `${INPUT_REVIEWDOG_FLAGS} |` (passed as unquoted positional args to `reviewdog`). These should be quoted or handled via an array.

Locations:

- `script.sh:49`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, permissions

**Notes:**

Fixed script injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} expansions with bash arrays (read -ra + "${array[@]}" expansion), preventing shell metacharacter injection. Added minimal top-level permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (checks:write, contents:read, pull-requests:write), test.yml (checks:write, contents:read, pull-requests:write).

