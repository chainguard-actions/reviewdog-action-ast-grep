<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh (the supporting script run by the composite action's run step) expands workflow-controlled inputs without quotes, as bare command arguments rather than the exempt shellcheck-annotated array-assignment form. Line 58: `${INPUT_SG_FLAGS} |` (from inputs.sg_flags) and line 68: `${INPUT_REVIEWDOG_FLAGS} |` (from inputs.reviewdog_flags). The values are split on whitespace and glob-expanded straight into the ast-grep and reviewdog argument lists. The `# shellcheck disable=SC2086` on line 53 covers the whole pipeline, but these are not NAME=($VAR) array assignments, so the exemption does not apply. Fix: put each value into an array first with a directive, e.g. `# shellcheck disable=SC2206` then `SG_FLAGS=(${INPUT_SG_FLAGS})`, and pass `"${SG_FLAGS[@]}"`. Do the same for REVIEWDOG_FLAGS.

Locations:

- `script.sh:58`
- `script.sh:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

I changed script.sh so the inputs.sg_flags and inputs.reviewdog_flags values are each loaded into a bash array first, with a shellcheck SC2206 note: SG_FLAGS=(${INPUT_SG_FLAGS}) and REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS}). The ast-grep and reviewdog commands now take "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". Nothing has been run or tested. The arrays split on whitespace and expand globs the same way upstream's unquoted variables did, so flag lists like '--filter rule path' still reach the tools as separate arguments. An empty input gives no arguments at all. The script is bash (#!/bin/bash) and the step runs it with shell: bash, so arrays work. The script doesn't use set -u, so an empty array is also safe on macOS's older bash 3.2. I did not change action.yml.

