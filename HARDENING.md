<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh is run by the composite step in action.yml ('run: $GITHUB_ACTION_PATH/script.sh'). It expands two workflow-controlled env vars unquoted, straight into command arguments: '${INPUT_SG_FLAGS} |' (from inputs.sg_flags) in the ast-grep scan command, and '${INPUT_REVIEWDOG_FLAGS} |' (from inputs.reviewdog_flags) in the reviewdog command. The '# shellcheck disable=SC2086' comment sits on a command, not on a NAME=($VAR) array assignment, so the array-list exception does not apply. Fix: split each value into an array first with the hardened form, e.g. '# shellcheck disable=SC2206' then 'SG_FLAGS=(${INPUT_SG_FLAGS})', and pass the array as "${SG_FLAGS[@]}". Do the same for REVIEWDOG_FLAGS.

Locations:

- `script.sh:58`
- `script.sh:66`
- `action.yml:69`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

script.sh: INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now split into arrays with SG_FLAGS=(${INPUT_SG_FLAGS}) and REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS}), each with a '# shellcheck disable=SC2206' comment. The commands now take "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". This splits on whitespace and expands globs the same way upstream's unquoted variables did, and an empty input still adds no argument. reviewdog still reads its data on stdin from the pipe. action.yml: the composite step now runs the script with the path in double quotes ("$GITHUB_ACTION_PATH/script.sh"). That step uses shell: bash, so the quoting is valid. Nothing was run or tested.

