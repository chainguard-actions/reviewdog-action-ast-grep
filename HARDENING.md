<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action steps in action.yml pin actions to version tags instead of full 40-character commit SHAs: 'uses: reviewdog/action-setup@v1.5.0' and 'uses: github/codeql-action/upload-sarif@v4.39.1'.

Locations:

- `action.yml:67`
- `action.yml:91`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned reviewdog/action-setup to d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f (v1.5.0) and github/codeql-action/upload-sarif to 9b3254085494b55a0f7c4c2b3d2d52cc712ce370 (v4.39.1) in action.yml. No other changes.

