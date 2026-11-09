<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite action steps reference actions by a version tag instead of a full 40-character commit SHA: 'uses: reviewdog/action-setup@v1.5.0' and 'uses: github/codeql-action/upload-sarif@v4.39.1'.

Locations:

- `action.yml:57`
- `action.yml:82`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned reviewdog/action-setup@v1.5.0 to d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f and github/codeql-action/upload-sarif@v4.39.1 to 9b3254085494b55a0f7c4c2b3d2d52cc712ce370, keeping tag comments.

