<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.13** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh is the supporting script run by the composite step in action.yml. It expands two env vars that come from workflow inputs without quotes, as command arguments: line 57 `${INPUT_SG_FLAGS} |` (from inputs.sg_flags, passed to `ast-grep scan`) and line 67 `${INPUT_REVIEWDOG_FLAGS} |` (from inputs.reviewdog_flags, passed to `reviewdog`). These are not array assignments of the form NAME=($VAR), so the '# shellcheck disable=SC2086' exemption does not apply. Because the values are not quoted, they are split on whitespace and glob characters in them are expanded. Fix: split each list once into an array using the hardened form with a '# shellcheck disable=SC2086' comment, e.g. SG_FLAGS=(${INPUT_SG_FLAGS}), then pass it as "${SG_FLAGS[@]}". Do the same for INPUT_REVIEWDOG_FLAGS.

Locations:

- `script.sh:57`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

I fixed the script-injection finding in script.sh. The two inputs that upstream passed without quotes (`${INPUT_SG_FLAGS}` to `ast-grep scan` and `${INPUT_REVIEWDOG_FLAGS}` to `reviewdog`) are now each split once into an array, `SG_FLAGS=(${INPUT_SG_FLAGS})` and `REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS})`, with `# shellcheck disable=SC2086` comments. The commands now get `"${SG_FLAGS[@]}"` and `"${REVIEWDOG_FLAGS[@]}"`. Splitting and glob expansion work exactly as upstream's did, an empty input still adds no arguments, and the pipes (reviewdog still reads ast-grep's output) are unchanged. The script already runs under bash, so the array syntax is safe. I did not run the action to test this.

