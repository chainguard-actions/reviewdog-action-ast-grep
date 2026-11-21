<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.70.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.70.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): workflow-controlled inputs are expanded without quotes in script.sh, which action.yml runs from its composite step. Line 58 passes `${INPUT_SG_FLAGS} |` to `ast-grep scan`, and line 67 passes `${INPUT_REVIEWDOG_FLAGS} |` to `reviewdog`. Both come from inputs.sg_flags and inputs.reviewdog_flags through env. The `# shellcheck disable=SC2086` directive is on a direct command argument, not on an array assignment of the form NAME=($VAR), so the exemption does not apply. Fix: put each value into an array on its own line, preceded by `# shellcheck disable=SC2086,SC2206` (for example `SG_FLAGS=(${INPUT_SG_FLAGS})`), and pass the array as "${SG_FLAGS[@]}". Do the same for REVIEWDOG_FLAGS.

Locations:

- `script.sh:58`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

In script.sh, inputs.sg_flags and inputs.reviewdog_flags were passed straight into the commands unquoted. Each is now split into a bash array with an unquoted assignment, SG_FLAGS=(${INPUT_SG_FLAGS}) and REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS}), each marked with '# shellcheck disable=SC2086,SC2206'. The arrays are passed as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". This splits on whitespace and expands globs exactly as upstream did, and an empty input adds no argument. The pipeline is unchanged, including PIPESTATUS. The script has no set -u, so empty arrays also work in macOS's bash 3.2. Not run or tested.

