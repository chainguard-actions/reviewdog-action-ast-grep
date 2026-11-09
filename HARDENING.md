<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.6** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh, which is run by action.yml's composite run step, expands two workflow-controlled env vars without quotes as command arguments. These are `${INPUT_SG_FLAGS} |` on line 57 (from inputs.sg_flags) and `${INPUT_REVIEWDOG_FLAGS} |` on line 67 (from inputs.reviewdog_flags). A '# shellcheck disable=SC2086' comment sits above the pipeline, but these are not NAME=($VAR) array assignments, so the exemption does not apply. Recommended fix: split each value into an array on a line marked '# shellcheck disable=SC2086' (e.g. SG_FLAGS=(${INPUT_SG_FLAGS})), then pass the array quoted, as "${SG_FLAGS[@]}".

Locations:

- `script.sh:57`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

In script.sh, `${INPUT_SG_FLAGS}` and `${INPUT_REVIEWDOG_FLAGS}` are now split into arrays with SG_FLAGS=(${INPUT_SG_FLAGS}) and REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS}). Each assignment has a '# shellcheck disable=SC2086' line above it. The arrays are passed quoted as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". This keeps upstream's whitespace split and glob behaviour. It does not use xargs, because reviewdog reads its input on stdin.

