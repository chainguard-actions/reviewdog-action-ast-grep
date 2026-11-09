<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.11

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.11** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation in script.sh, which the composite action's run step calls. Two variables are expanded unquoted as command arguments: `${INPUT_SG_FLAGS} |` (passed to `ast-grep scan`) and `${INPUT_REVIEWDOG_FLAGS} |` (passed to `reviewdog`). They come from inputs.sg_flags and inputs.reviewdog_flags, so the calling workflow controls them. Neither uses the exempt form `NAME=($VAR)` with `# shellcheck disable=SC2086`. The existing shellcheck comment sits above a pipeline, not an array assignment. Suggested fix: split each value into an array, e.g. `# shellcheck disable=SC2086` then `SG_FLAGS=(${INPUT_SG_FLAGS})`, and pass it as `"${SG_FLAGS[@]}"`. Do the same for the reviewdog flags.

Locations:

- `script.sh:57`
- `script.sh:66`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

In script.sh, the `sg_flags` and `reviewdog_flags` values are now split into bash arrays with `SG_FLAGS=(${INPUT_SG_FLAGS})` and `REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS})`, each marked with `# shellcheck disable=SC2086`. The commands then receive them as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". This splits on whitespace and expands globs exactly as upstream's unquoted expansion did, and an empty input still adds no argument. The script's shebang is #!/bin/bash and the composite step runs it with `shell: bash`, so the array syntax will run. I didn't use xargs, since reviewdog reads its input from the pipe on stdin. Nothing else changed. I haven't run the action or a new analysis pass after the edit.

