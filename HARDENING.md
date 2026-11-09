<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.58

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.58** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh is the supporting script run by the composite step in action.yml. It expands two workflow-controlled env vars unquoted as plain command arguments: line 56 `  ${INPUT_SG_FLAGS} |` (from inputs.sg_flags), passed to `ast-grep scan`, and line 66 `    ${INPUT_REVIEWDOG_FLAGS} |` (from inputs.reviewdog_flags), passed to `reviewdog`. There is a `# shellcheck disable=SC2086` comment, but these are not the exempt hardened form (an array assignment such as SG_FLAGS=($INPUT_SG_FLAGS) with the shellcheck comment, then "${SG_FLAGS[@]}"). So the values are still split and glob-expanded straight into argv.

Locations:

- `script.sh:56`
- `script.sh:66`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

In script.sh (a #!/bin/bash script run under shell: bash), I changed how the two flag inputs are passed. INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now each assigned unquoted into an array, SG_FLAGS=(${INPUT_SG_FLAGS}) and REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS}), with a shellcheck disable comment. The commands now receive them as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". Splitting and globbing work exactly as they did upstream, and an empty input still adds no argument. ast-grep's pipe into reviewdog's stdin is unchanged. I didn't need to change action.yml. Not tested: the action wasn't run after the change.

