<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.57.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.57.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh, which action.yml's run: step calls, expands two workflow-controlled inputs without quotes, straight into command arguments: `${INPUT_SG_FLAGS}` (from inputs.sg_flags) on line 57, passed to `ast-grep scan`, and `${INPUT_REVIEWDOG_FLAGS}` (from inputs.reviewdog_flags) on line 67, passed to `reviewdog`. A '# shellcheck disable=SC2086' comment is present, but these are not NAME=($VAR) array assignments, so the hardened-list exemption does not apply. The values are split on whitespace and glob-expanded without control. Fix: split them into arrays using the exempt form (e.g. `# shellcheck disable=SC2086` on the line above `SG_FLAGS=($INPUT_SG_FLAGS)`), then pass them as "${SG_FLAGS[@]}".

Locations:

- `script.sh:57`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

script.sh: INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now split into arrays with the unquoted assignment form, each with '# shellcheck disable=SC2086' on the line above: SG_FLAGS=($INPUT_SG_FLAGS) and REVIEWDOG_FLAGS=($INPUT_REVIEWDOG_FLAGS). They are passed as "${SG_FLAGS[@]}" and "${REVIEWDOG_FLAGS[@]}". The flags split on whitespace and expand globs just as upstream's unquoted expansion did, and an empty value adds no argument. The script uses bash and does not set -u, so this also works on the bash 3.2 that ships on macOS. The pipeline layout and the PIPESTATUS index are unchanged.

