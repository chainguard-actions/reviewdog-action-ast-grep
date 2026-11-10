<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.56.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.56.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): in script.sh (run by the composite step in action.yml), the input-sourced env vars INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are expanded unquoted straight into command arguments: `${INPUT_SG_FLAGS} |` (line 59) and `${INPUT_REVIEWDOG_FLAGS} |` (line 68). The `# shellcheck disable=SC2086` comment sits on a command, not on a NAME=($VAR) array assignment, so the array exemption does not cover it. The unquoted values are word-split and glob-expanded into arbitrary arguments for ast-grep and reviewdog. Fix: split each value into an array on its own line with the shellcheck directive (e.g. `# shellcheck disable=SC2206` then `SG_FLAGS=(${INPUT_SG_FLAGS})`) and pass it as "${SG_FLAGS[@]}". Do the same for the reviewdog flags.

Locations:

- `script.sh:59`
- `script.sh:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

script.sh: INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now each split into an array (SG_FLAGS=(${INPUT_SG_FLAGS}), REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS})) on its own line with '# shellcheck disable=SC2206'. They are passed as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". The split and glob are the same as upstream's unquoted expansion, so multi-word flag lists still reach the tools as separate arguments.

