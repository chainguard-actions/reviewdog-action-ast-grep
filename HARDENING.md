<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh, which the composite step runs, expands two values from workflow inputs unquoted as command arguments. Line 57 passes `${INPUT_SG_FLAGS}` (from inputs.sg_flags) to `ast-grep scan`. Line 67 passes `${INPUT_REVIEWDOG_FLAGS}` (from inputs.reviewdog_flags) to `reviewdog`. The '# shellcheck disable=SC2086' comment sits on a command, not on an array assignment, so the array exemption does not apply. Suggested fix, which keeps the whitespace-separated list behaviour: assign each value to an array first, with the shellcheck comment on the line above, e.g. `SG_FLAGS=(${INPUT_SG_FLAGS})` and `RD_FLAGS=(${INPUT_REVIEWDOG_FLAGS})`, then pass "${SG_FLAGS[@]}" and "${RD_FLAGS[@]}".

Locations:

- `script.sh:57`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

In script.sh, INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now each loaded into a bash array (SG_FLAGS=(${INPUT_SG_FLAGS}), RD_FLAGS=(${INPUT_REVIEWDOG_FLAGS})), with shellcheck disable comments on the assignment lines. The arrays are passed to ast-grep and reviewdog as ${ARR[@]+"${ARR[@]}"}, which is safe on bash 3.2 (macOS) when an array is empty. Splitting and globbing work as upstream's did, and the pipeline layout is unchanged, so reviewdog still reads from stdin and the PIPESTATUS index stays the same.

