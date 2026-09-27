<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.57.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.57.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of user-controlled inputs appear in command positions in script.sh, allowing shell metacharacter injection.

1. Line 48: `  ${INPUT_SG_FLAGS} |` — the `inputs.sg_flags` value is expanded without double-quotes directly as arguments to `ast-grep scan`. An attacker-controlled value containing `;`, `|`, `$(...)`, or other shell metacharacters will be interpreted by the shell. The `# shellcheck disable=SC2086` comment on line 46 explicitly suppresses the warning about this unquoted expansion.

2. Line 57: `    ${INPUT_REVIEWDOG_FLAGS} |` — the `inputs.reviewdog_flags` value is expanded without double-quotes as arguments to `reviewdog`, with the same injection risk.

Both variables are sourced from `inputs.*` (set via `env:` in action.yml) and must be double-quoted or handled with the guarded form `${VAR:+"$VAR"}` to prevent word-splitting and metacharacter injection.

Locations:

- `script.sh:48`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed both unquoted variable expansions in script.sh (lines 48 and 57). INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now tokenized into bash arrays using the xargs-based pattern (with NUL delimiters and guarded by `if [ -n ... ]`) before being used in the pipeline. Arrays are expanded with `"${sg_flags[@]}"` and `"${reviewdog_flags[@]}"` to keep each token as a separate argument without shell metacharacter interpretation. The reviewdog_flags array is built before the pipeline to avoid stealing reviewdog's stdin. The `# shellcheck disable=SC2086` comment was removed as it's no longer needed.

