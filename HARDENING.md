<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.70.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.70.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh, which the action's run: step calls, expands the workflow-controlled inputs INPUT_SG_FLAGS (line 58, `${INPUT_SG_FLAGS} |`) and INPUT_REVIEWDOG_FLAGS (line 67, `${INPUT_REVIEWDOG_FLAGS} |`) without quotes, directly as command arguments. The '# shellcheck disable=SC2086' exemption covers only unquoted expansion into an array assignment (NAME=($VAR)). It does not cover an inline command argument. Fix: split each value into an array first, keeping the directive (e.g. `# shellcheck disable=SC2086,SC2206` then `SG_FLAGS=(${INPUT_SG_FLAGS})`), and pass it as "${SG_FLAGS[@]}". Do the same for REVIEWDOG_FLAGS.

Locations:

- `script.sh:58`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

script.sh: before the ast-grep/reviewdog pipeline, INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now split into the arrays SG_FLAGS and REVIEWDOG_FLAGS using unquoted array assignments (with '# shellcheck disable=SC2086,SC2206'). They are passed as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". The values are split on whitespace and glob-expanded exactly as upstream's unquoted expansion did, and an empty value adds no argument. reviewdog still reads from stdin because nothing is piped through xargs. I haven't run the script. The script runs under bash, so it uses no set -u and is safe on bash 3.2.

