<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of workflow-controllable inputs in script.sh. `${INPUT_SG_FLAGS}` (line 57) and `${INPUT_REVIEWDOG_FLAGS}` (line 67) are expanded without double-quotes. These variables are populated from `inputs.sg_flags` and `inputs.reviewdog_flags` (both `required: false`, `default: ''`) via the `env:` block in action.yml. An attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) would be parsed by the shell, enabling command injection. The correct safe form for optional positional arguments is the guarded expansion `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` and `${INPUT_REVIEWDOG_FLAGS:+"$INPUT_REVIEWDOG_FLAGS"}` respectively.

Locations:

- `script.sh:57`
- `script.sh:67`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two unquoted shell variable expansions in hardened/action/script.sh:
1. Line 57: Changed `${INPUT_SG_FLAGS}` to `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` - uses guarded expansion so the variable is omitted when empty and properly quoted when set.
2. Line 67: Changed `${INPUT_REVIEWDOG_FLAGS}` to `${INPUT_REVIEWDOG_FLAGS:+"$INPUT_REVIEWDOG_FLAGS"}` - same guarded expansion pattern.
Also removed the now-unnecessary `# shellcheck disable=SC2086` comment that was suppressing the warning about the previously unquoted expansions. Note: The xargs tokenization approach was not used for INPUT_REVIEWDOG_FLAGS because reviewdog reads from stdin in the pipeline, and piping through xargs would break stdin consumption.

