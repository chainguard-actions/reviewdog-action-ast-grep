<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.70.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.70.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh is run by the composite step in action.yml, and it expands two input-controlled env vars without quotes as command arguments: `${INPUT_SG_FLAGS} |` (line 58, from inputs.sg_flags) and `${INPUT_REVIEWDOG_FLAGS} |` (line 68, from inputs.reviewdog_flags). The array-assignment exemption doesn't cover these. They are plain unquoted expansions on a command line, not NAME=($VAR) assignments, so the shell splits them on spaces and expands wildcard (glob) characters in them. The `# shellcheck disable=SC2086` comment does not make them exempt. Fix: build arrays first, e.g. `# shellcheck disable=SC2206` then `SG_FLAGS=(${INPUT_SG_FLAGS})` and `REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS})`, and pass them as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}".

Locations:

- `script.sh:58`
- `script.sh:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

I fixed the script-injection finding in script.sh. Before the `ast-grep scan | reviewdog` pipeline, `INPUT_SG_FLAGS` and `INPUT_REVIEWDOG_FLAGS` are now put into arrays with unquoted assignments (`SG_FLAGS=(${INPUT_SG_FLAGS})`, `REVIEWDOG_FLAGS=(...)`, each with `# shellcheck disable=SC2206`). This splits the values on whitespace and expands globs the same way upstream's unquoted `$VAR` did, and an empty input adds no arguments. The arrays are expanded as `${ARR[@]+"${ARR[@]}"}` so an empty array is still safe in bash 3.2 (macOS) if `set -u` is ever on. I did not use xargs, so quotes in the values are handled as before and reviewdog still reads ast-grep's output on stdin. Nothing was run or tested. action.yml is unchanged.

