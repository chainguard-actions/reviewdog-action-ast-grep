<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): in script.sh, which the composite step runs, the workflow-controlled env vars INPUT_SG_FLAGS (from inputs.sg_flags) and INPUT_REVIEWDOG_FLAGS (from inputs.reviewdog_flags) are expanded unquoted straight into command arguments: line 57 `${INPUT_SG_FLAGS} |` (ast-grep scan) and line 67 `${INPUT_REVIEWDOG_FLAGS} |` (reviewdog). The '# shellcheck disable=SC2086' comment is there, but these are not array assignments (NAME=($VAR)), so the exemption does not apply. Word splitting and glob expansion of caller-supplied values can inject extra arguments. The fix is to assign each value to an array on its own line, e.g. `# shellcheck disable=SC2086` then `SG_FLAGS=(${INPUT_SG_FLAGS})`, and pass "${SG_FLAGS[@]}".

Locations:

- `script.sh:57`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

I fixed the script-injection finding in script.sh. Nothing has been run or tested.

The two flag inputs are no longer pasted straight into the commands. Each one is now put into a bash array on its own line, with the shellcheck SC2086 exception comment above it:
- `SG_FLAGS=(${INPUT_SG_FLAGS})`
- `REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS})`

The commands then get `"${SG_FLAGS[@]}"` and `"${REVIEWDOG_FLAGS[@]}"`. The unquoted array assignment breaks the value into arguments on whitespace and expands file patterns exactly as upstream did, and an empty input still adds no argument. So `sg_flags: --filter rule path` still reaches ast-grep as separate arguments.

The arrays work because the script starts with `#!/bin/bash` and the step uses `shell: bash`. The plain `"${ARR[@]}"` form is safe on macOS's older bash 3.2 because the script doesn't use `set -u`. ast-grep and reviewdog still pass data to each other through the pipe as before. No other files were changed.

