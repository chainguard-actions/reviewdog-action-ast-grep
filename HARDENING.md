<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.68.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.68.3** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite action steps in action.yml use mutable version tags instead of full 40-character commit SHAs: 'uses: reviewdog/action-setup@v1.5.0' (line 63) and 'uses: github/codeql-action/upload-sarif@v4.39.1' (line 87). Pin each one to a commit SHA and keep the version in a trailing comment.

Locations:

- `action.yml:63`
- `action.yml:87`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned reviewdog/action-setup@v1.5.0 to d8a7baabd7f3e8544ee4dbde3ee41d0011c3a93f and github/codeql-action/upload-sarif@v4.39.1 to 9b3254085494b55a0f7c4c2b3d2d52cc712ce370 in action.yml.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

In script.sh, INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now each split into a bash array: SG_FLAGS=($INPUT_SG_FLAGS) and REVIEWDOG_FLAGS=($INPUT_REVIEWDOG_FLAGS). Each assignment sits on its own line with a '# shellcheck disable=SC2086' directive. The commands then take "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". This splits on whitespace and expands globs exactly as upstream's unquoted expansion did, and an empty input produces no argument at all. The pipeline layout and the PIPESTATUS[1] exit-code handling are unchanged. Nothing was run to test this; the script uses bash, which a composite 'shell: bash' step provides.

