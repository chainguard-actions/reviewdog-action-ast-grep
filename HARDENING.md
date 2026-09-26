<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.9** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of workflow-controllable inputs appear in script.sh. (1) Line 49: `${INPUT_SG_FLAGS}` is passed unquoted as a positional argument to `ast-grep scan`. This variable holds `inputs.sg_flags` (set via `${{ inputs.sg_flags }}` in action.yml). An attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) will be word-split and interpreted by the shell before being passed to the command. (2) Line 58: `${INPUT_REVIEWDOG_FLAGS}` is passed unquoted to `reviewdog`. This variable holds `inputs.reviewdog_flags`. Both should use the guarded form `${VAR:+"$VAR"}` (since they are optional flags) or be double-quoted as `"${VAR}"` to prevent shell metacharacter injection.

Locations:

- `script.sh:49`
- `script.sh:58`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two unquoted shell variable expansions in script.sh:
1. Line 49: ${INPUT_SG_FLAGS} - tokenized into bash array 'sg_flags' using xargs with NUL delimiters and a guard for empty input, then expanded as "${sg_flags[@]}" in the ast-grep scan command.
2. Line 58: ${INPUT_REVIEWDOG_FLAGS} - tokenized into bash array 'reviewdog_flags' using the same xargs pattern (built before the pipeline so reviewdog still reads from stdin), then expanded as "${reviewdog_flags[@]}" in the reviewdog command.
Both fixes use the required 'if [ -n "$VAR" ]' guard to prevent xargs from emitting an empty token on empty input, and 'printf "%s"' before the pipe to handle values starting with '-'.

