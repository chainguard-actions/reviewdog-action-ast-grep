<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of workflow-controllable inputs in script.sh allow shell metacharacter injection. (1) Line 52: `${INPUT_SG_FLAGS}` is an unquoted expansion of `inputs.sg_flags` passed as positional flags to `ast-grep scan`. (2) Line 61: `${INPUT_REVIEWDOG_FLAGS}` is an unquoted expansion of `inputs.reviewdog_flags` passed as positional flags to `reviewdog`. Both are optional inputs (default: '') but use bare `${VAR}` instead of the safe guarded form `${VAR:+"$VAR"}`, allowing an attacker to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via the input values. The `# shellcheck disable=SC2086` comment on line 50 suppresses the shellcheck warning but does not fix the vulnerability.

Locations:

- `script.sh:52`
- `script.sh:61`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed both unquoted variable expansions in script.sh:
1. Line 52: Replaced bare `${INPUT_SG_FLAGS}` with a bash array `sg_flags` built via xargs tokenization (with NUL delimiters and an empty-value guard), then expanded as `"${sg_flags[@]}"`.
2. Line 61: Replaced bare `${INPUT_REVIEWDOG_FLAGS}` with a bash array `reviewdog_flags` built the same way, then expanded as `"${reviewdog_flags[@]}"`. Since this is array expansion (not piping), reviewdog still receives its stdin from the jq pipeline.
3. Removed the `# shellcheck disable=SC2086` comment that was suppressing the warning about the unquoted expansions.

