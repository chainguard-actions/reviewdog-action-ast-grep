<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.60.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.60.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): two unquoted shell variable expansions of workflow-controlled inputs in script.sh, which the composite run step in action.yml calls. `${INPUT_SG_FLAGS}` (from inputs.sg_flags) is passed directly to `ast-grep scan`. `${INPUT_REVIEWDOG_FLAGS}` (from inputs.reviewdog_flags) is passed directly to `reviewdog`. The `# shellcheck disable=SC2086` comment does not exempt these, because they are bare command arguments, not array assignments (NAME=($VAR)). Fix: first build arrays such as `# shellcheck disable=SC2086` `SG_FLAGS=(${INPUT_SG_FLAGS})` and `REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS})`, then pass them as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}".

Locations:

- `script.sh:57`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed the script-injection finding in script.sh. Before running the pipeline, the script now loads `INPUT_SG_FLAGS` and `INPUT_REVIEWDOG_FLAGS` into arrays with unquoted assignment (`SG_FLAGS=(${INPUT_SG_FLAGS})`, with a shellcheck SC2086 disable comment). `ast-grep scan` and `reviewdog` now receive them as `"${SG_FLAGS[@]}"` and `"${REVIEWDOG_FLAGS[@]}"`. The flags split on whitespace and expand glob patterns exactly as they did upstream. An empty input adds no argument. reviewdog still reads its data from the pipe. action.yml needed no changes.

