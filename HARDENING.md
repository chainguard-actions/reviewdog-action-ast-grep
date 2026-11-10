<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.58.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.58.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation in script.sh, which the composite step runs ($GITHUB_ACTION_PATH/script.sh). Two env vars that come straight from action inputs are expanded unquoted as command arguments: `${INPUT_SG_FLAGS} |` on the ast-grep scan command (INPUT_SG_FLAGS = inputs.sg_flags) and `${INPUT_REVIEWDOG_FLAGS} |` on the reviewdog command (INPUT_REVIEWDOG_FLAGS = inputs.reviewdog_flags). The '# shellcheck disable=SC2086' directive is there, but these are not NAME=($VAR) array assignments, so the hardened-list exception does not cover them. Fix: split each value into an array on its own line under the directive, e.g. `# shellcheck disable=SC2206` / `SG_FLAGS=(${INPUT_SG_FLAGS})`, and do the same for REVIEWDOG_FLAGS. Then pass them as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}".

Locations:

- `script.sh:57`
- `script.sh:66`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

script.sh: INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now split into arrays with SG_FLAGS=(${INPUT_SG_FLAGS}) and REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS}), each under a shellcheck disable=SC2086,SC2206 directive. The commands now pass "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". Arguments split and glob the same way as upstream's unquoted expansion. Each command still reads its data from the pipe on stdin.

