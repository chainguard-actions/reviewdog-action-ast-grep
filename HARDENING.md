<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.61.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.61.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of user-controlled inputs appear in script.sh. `${INPUT_SG_FLAGS}` (line 48) and `${INPUT_REVIEWDOG_FLAGS}` (line 57) are sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (set via `env:` in action.yml from `${{ inputs.sg_flags }}` and `${{ inputs.reviewdog_flags }}`). Because these expansions are unquoted, a caller can inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) to execute arbitrary commands. The `# shellcheck disable=SC2086` comment suppresses the linter warning but does not mitigate the security risk. The safe form for optional flag arguments is `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` or quoting the value and using an array.

Locations:

- `script.sh:48`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed both unquoted expansions in script.sh: replaced `${INPUT_SG_FLAGS}` (line 48) and `${INPUT_REVIEWDOG_FLAGS}` (line 57) with safe bash array tokenization using the xargs/NUL-delimited read-loop pattern. Each input is guarded with `if [ -n "$VAR" ]` to prevent empty-token injection, tokenized via `printf '%s' "$VAR" | xargs printf '%s\0'`, and expanded as `"${array[@]}"` to keep each flag as a separate, properly-quoted argument. The `# shellcheck disable=SC2086` comment was removed as it is no longer needed.

