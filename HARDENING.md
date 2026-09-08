<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no job within any of these files defines job-level `permissions:` either. Without explicit permissions, workflows inherit the repository's default token permissions, which may be overly broad (e.g., write access to contents, pull-requests, etc.).

Locations:

- `.github/workflows/depup.yml:1`
- `.github/workflows/labels.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Rule (b) violation: In script.sh (invoked by the composite action step in action.yml), two user-controlled input variables are expanded without double-quoting, allowing shell metacharacter injection. `${INPUT_SG_FLAGS}` (from `inputs.sg_flags`) is used unquoted on the `ast-grep scan` command line (line 52: `  ${INPUT_SG_FLAGS} |`), and `${INPUT_REVIEWDOG_FLAGS}` (from `inputs.reviewdog_flags`) is used unquoted on the `reviewdog` command line (line 60: `    ${INPUT_REVIEWDOG_FLAGS} |`). An attacker-controlled caller can inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) through these inputs. The `# shellcheck disable=SC2086` comment confirms intentional unquoted expansion, but this does not mitigate the injection risk. Both variables should be double-quoted: `"${INPUT_SG_FLAGS}"` and `"${INPUT_REVIEWDOG_FLAGS}"`, or handled via an array.

Locations:

- `script.sh:52`
- `script.sh:60`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} expansions with xargs-based bash array tokenization (quote-aware word splitting). Added top-level permissions blocks to all 5 workflow files with minimal required permissions: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (checks:write, contents:read, pull-requests:write), test.yml (checks:write, contents:read, pull-requests:write).

