<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.65.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.65.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of user-controlled inputs appear in script.sh. `${INPUT_SG_FLAGS}` (line 52) and `${INPUT_REVIEWDOG_FLAGS}` (line 62) are expanded without double-quoting and without the guarded `${VAR:+"$VAR"}` form. These variables hold values sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (both set via `env:` from `${{ inputs.sg_flags }}` / `${{ inputs.reviewdog_flags }}` in action.yml). An attacker-controlled caller can inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) through these inputs, achieving arbitrary command execution on the runner. The `# shellcheck disable=SC2086` comment confirms intentional word-splitting, but the correct safe form for optional flag inputs is `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` or proper array handling.

Locations:

- `script.sh:52`
- `script.sh:62`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two unquoted shell variable expansions in script.sh: ${INPUT_SG_FLAGS} (line 52) and ${INPUT_REVIEWDOG_FLAGS} (line 62). Both were replaced with bash arrays populated using the xargs-based NUL-delimited tokenization idiom (with guards and xargs -r). The arrays are expanded with "${sg_flags[@]}" and "${reviewdog_flags[@]}" respectively. The # shellcheck disable=SC2086 comment was removed as it is no longer needed. The reviewdog flags use arrays rather than piped xargs since reviewdog reads from stdin in the pipeline.

