<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): two inputs set by the calling workflow are expanded unquoted in script.sh, which the composite action's run step executes. Line 57 has `${INPUT_SG_FLAGS} |` (from inputs.sg_flags) as arguments to `ast-grep scan`. Line 67 has `${INPUT_REVIEWDOG_FLAGS} |` (from inputs.reviewdog_flags) as arguments to `reviewdog`. Neither is the exempt array-assignment form NAME=($VAR). Both are bare expansions inside a command, so the shell splits the values on whitespace and expands glob characters in them. Fix: build arrays with the exempt hardened form, e.g. `# shellcheck disable=SC2086` then `SG_FLAGS=(${INPUT_SG_FLAGS})`, and pass them as "${SG_FLAGS[@]}". Do the same for INPUT_REVIEWDOG_FLAGS.

Locations:

- `script.sh:57`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

In script.sh, the inputs `INPUT_SG_FLAGS` and `INPUT_REVIEWDOG_FLAGS` are now loaded into arrays with unquoted assignment (`SG_FLAGS=(${INPUT_SG_FLAGS})`, `REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS})`, with a shellcheck disable comment). The arrays are then passed to the commands as `${ARR[@]+"${ARR[@]}"}`, so an empty input adds no argument, even on bash 3.2 with `set -u`. Values are still split on whitespace and globbed exactly as upstream did, and `PIPESTATUS[1]` still gives reviewdog's exit code. Nothing was run to test this.

